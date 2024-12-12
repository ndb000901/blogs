# IO多路复用3-epoll

### `epoll`详解

`epoll` 是 Linux 下高效的多路复用 I/O 模型，专为大规模并发场景设计，相较于 `select` 和 `poll`，在监控大量文件描述符时性能更优。

---

### `epoll` 工作模式

`epoll` 采用基于事件通知的机制，提供以下两种工作模式：

1. **边缘触发 (Edge-Triggered, ET)**：
   - 只有当文件描述符状态从未就绪变为就绪时才通知用户。
   - 高效，但需要非阻塞 I/O，避免丢事件。

2. **水平触发 (Level-Triggered, LT)**：
   - 文件描述符处于就绪状态时会反复通知用户，直到条件清除。
   - 类似于 `select` 和 `poll`，使用简单。

---

### `epoll` 的核心函数

1. **`epoll_create` 或 `epoll_create1`**:
   - 创建 `epoll` 实例，返回一个 `epoll` 文件描述符。

2. **`epoll_ctl`**:
   - 用于管理 `epoll` 实例中的文件描述符。
   - 操作类型：
     - `EPOLL_CTL_ADD`: 添加文件描述符。
     - `EPOLL_CTL_MOD`: 修改文件描述符的监控事件。
     - `EPOLL_CTL_DEL`: 删除文件描述符。

3. **`epoll_wait`**:
   - 等待事件触发并获取就绪的文件描述符。

---

### 核心数据结构

#### `epoll_event` 结构体

```c
struct epoll_event {
    uint32_t events;    // 事件类型
    epoll_data_t data;  // 用户数据
};

typedef union epoll_data {
    void *ptr;
    int fd;
    uint32_t u32;
    uint64_t u64;
} epoll_data_t;
```

- **`events`**：监控的事件类型：
  - `EPOLLIN`: 数据可读。
  - `EPOLLOUT`: 数据可写。
  - `EPOLLERR`: 错误。
  - `EPOLLRDHUP`: 对端关闭。
  - `EPOLLET`: 边缘触发模式。

- **`data`**：用户定义的数据，用于标识事件（例如保存文件描述符）。

---

### 示例代码：使用 `epoll` 实现 TCP Echo 服务器

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/epoll.h>
#include <fcntl.h>

#define PORT 8080
#define MAX_EVENTS 1024
#define BUFFER_SIZE 1024

// 设置文件描述符为非阻塞模式
void set_nonblocking(int fd) {
    int flags = fcntl(fd, F_GETFL, 0);
    if (flags == -1) {
        perror("fcntl F_GETFL");
        exit(EXIT_FAILURE);
    }
    if (fcntl(fd, F_SETFL, flags | O_NONBLOCK) == -1) {
        perror("fcntl F_SETFL");
        exit(EXIT_FAILURE);
    }
}

int main() {
    int server_fd, client_fd, epoll_fd;
    struct sockaddr_in server_addr, client_addr;
    socklen_t addr_len = sizeof(client_addr);
    struct epoll_event ev, events[MAX_EVENTS];
    char buffer[BUFFER_SIZE];
    int i;

    // 创建监听套接字
    server_fd = socket(AF_INET, SOCK_STREAM, 0);
    if (server_fd == -1) {
        perror("socket");
        exit(EXIT_FAILURE);
    }

    // 绑定地址和端口
    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = INADDR_ANY;
    server_addr.sin_port = htons(PORT);

    if (bind(server_fd, (struct sockaddr *)&server_addr, sizeof(server_addr)) == -1) {
        perror("bind");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    // 开始监听
    if (listen(server_fd, 10) == -1) {
        perror("listen");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    printf("Echo server listening on port %d...\n", PORT);

    // 设置监听套接字为非阻塞
    set_nonblocking(server_fd);

    // 创建 epoll 实例
    epoll_fd = epoll_create1(0);
    if (epoll_fd == -1) {
        perror("epoll_create1");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    // 添加监听套接字到 epoll 实例
    ev.events = EPOLLIN;
    ev.data.fd = server_fd;
    if (epoll_ctl(epoll_fd, EPOLL_CTL_ADD, server_fd, &ev) == -1) {
        perror("epoll_ctl: server_fd");
        close(server_fd);
        close(epoll_fd);
        exit(EXIT_FAILURE);
    }

    while (1) {
        // 等待事件触发
        int nfds = epoll_wait(epoll_fd, events, MAX_EVENTS, -1);
        if (nfds == -1) {
            perror("epoll_wait");
            break;
        }

        // 遍历所有触发的事件
        for (i = 0; i < nfds; i++) {
            if (events[i].data.fd == server_fd) {
                // 处理新连接
                client_fd = accept(server_fd, (struct sockaddr *)&client_addr, &addr_len);
                if (client_fd == -1) {
                    perror("accept");
                    continue;
                }

                printf("New connection: socket fd is %d, IP is %s, port is %d\n",
                       client_fd,
                       inet_ntoa(client_addr.sin_addr),
                       ntohs(client_addr.sin_port));

                // 设置客户端套接字为非阻塞
                set_nonblocking(client_fd);

                // 添加客户端套接字到 epoll 实例
                ev.events = EPOLLIN | EPOLLET; // 边缘触发
                ev.data.fd = client_fd;
                if (epoll_ctl(epoll_fd, EPOLL_CTL_ADD, client_fd, &ev) == -1) {
                    perror("epoll_ctl: client_fd");
                    close(client_fd);
                    continue;
                }
            } else {
                // 处理客户端数据
                int sock_fd = events[i].data.fd;
                int bytes_read = read(sock_fd, buffer, BUFFER_SIZE - 1);
                if (bytes_read <= 0) {
                    // 客户端关闭或读取错误
                    printf("Client disconnected: socket fd is %d\n", sock_fd);
                    close(sock_fd);
                    epoll_ctl(epoll_fd, EPOLL_CTL_DEL, sock_fd, NULL);
                } else {
                    // 回显数据
                    buffer[bytes_read] = '\0';
                    printf("Echoing to client %d: %s\n", sock_fd, buffer);
                    send(sock_fd, buffer, bytes_read, 0);
                }
            }
        }
    }

    // 清理资源
    close(server_fd);
    close(epoll_fd);
    return 0;
}
```

---

### 代码解析

1. **非阻塞 I/O**:
   - `epoll` 尤其适合非阻塞 I/O，边缘触发模式下必须使用非阻塞套接字以避免丢事件。

2. **`epoll` 添加事件**:
   - `EPOLL_CTL_ADD` 添加文件描述符到 `epoll` 实例。
   - 边缘触发模式通过 `EPOLLET` 设置。

3. **动态处理文件描述符**:
   - 无需像 `select` 或 `poll` 遍历所有文件描述符，仅处理触发的事件。

---

### 优点与缺点

#### 优点
1. **高效**:
   - 触发式通知避免无效轮询。
   - 支持大量文件描述符，性能几乎与文件描述符数量无关。

2. **灵活**:
   - 支持边缘触发和水平触发模式。
   - 用户数据存储方便。

#### 缺点
1. **Linux 特有**:
   - 仅支持 Linux 系统，不具有跨平台性。

2. **复杂性**:
   - 边缘触发模式需要小心处理避免丢事件。

---

`epoll` 是目前 Linux 上实现高并发服务器的主流选择，特别适合大规模、多连接场景，结合非阻塞 I/O 使用效果更佳。