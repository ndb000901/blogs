# IO多路复用2-poll

### `poll`详解

`poll` 是 Linux 中一种比 `select` 更灵活的多路复用 I/O 机制，它用于同时监控多个文件描述符上的事件。相较于 `select`，`poll` 不再受文件描述符数量的限制，并且更易于动态扩展。

---

### `poll` 函数原型

```c
#include <poll.h>

int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```

#### 参数说明
1. **`fds`**:
   - 指向 `pollfd` 结构体数组的指针，每个数组元素代表一个文件描述符及其监控的事件。
   - 结构体定义：
     ```c
     struct pollfd {
         int fd;       // 文件描述符
         short events; // 监控的事件
         short revents; // 返回的事件
     };
     ```
   - `events` 表示希望监听的事件：
     - `POLLIN`: 数据可读。
     - `POLLOUT`: 数据可写。
     - `POLLERR`: 错误。
     - `POLLHUP`: 挂起。
     - `POLLPRI`: 紧急数据可读。
   - `revents` 用于返回实际发生的事件。

2. **`nfds`**:
   - 监控的文件描述符数量，即 `fds` 数组的大小。

3. **`timeout`**:
   - 等待超时时间（毫秒）。
     - `timeout = -1`: 无限期等待。
     - `timeout = 0`: 不等待，立即返回。
     - 正整数：指定的毫秒数。

#### 返回值
- **正数**: 有事件发生的文件描述符数量。
- **0**: 超时。
- **-1**: 错误（可以通过 `errno` 查看错误原因）。

---

### 使用场景
1. 监控多个套接字的可读、可写或错误事件。
2. 处理非阻塞 I/O，尤其是在并发连接数量较大时更高效。

---

### `poll` 示例代码

以下是一个使用 `poll` 实现的简单 TCP Echo 服务器：

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <poll.h>

#define PORT 8080
#define MAX_CLIENTS 1024
#define BUFFER_SIZE 1024

int main() {
    int server_fd, client_fd;
    struct sockaddr_in server_addr, client_addr;
    socklen_t addr_len = sizeof(client_addr);
    struct pollfd fds[MAX_CLIENTS];
    char buffer[BUFFER_SIZE];
    int i;

    // 初始化 pollfd 数组
    for (i = 0; i < MAX_CLIENTS; i++) {
        fds[i].fd = -1; // -1 表示未使用
    }

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

    // 将监听套接字添加到 pollfd 数组
    fds[0].fd = server_fd;
    fds[0].events = POLLIN; // 监听可读事件

    while (1) {
        // 调用 poll
        int activity = poll(fds, MAX_CLIENTS, -1); // 无限期等待
        if (activity < 0) {
            perror("poll");
            break;
        }

        // 处理监听套接字的事件
        if (fds[0].revents & POLLIN) {
            client_fd = accept(server_fd, (struct sockaddr *)&client_addr, &addr_len);
            if (client_fd < 0) {
                perror("accept");
                continue;
            }

            printf("New connection: socket fd is %d, IP is %s, port is %d\n",
                   client_fd,
                   inet_ntoa(client_addr.sin_addr),
                   ntohs(client_addr.sin_port));

            // 添加新客户端到 pollfd 数组
            for (i = 1; i < MAX_CLIENTS; i++) {
                if (fds[i].fd == -1) {
                    fds[i].fd = client_fd;
                    fds[i].events = POLLIN; // 监听可读事件
                    break;
                }
            }
        }

        // 处理客户端的事件
        for (i = 1; i < MAX_CLIENTS; i++) {
            if (fds[i].fd == -1) {
                continue;
            }

            if (fds[i].revents & POLLIN) {
                int bytes_read = read(fds[i].fd, buffer, BUFFER_SIZE - 1);
                if (bytes_read <= 0) {
                    // 客户端断开连接
                    printf("Client disconnected: socket fd is %d\n", fds[i].fd);
                    close(fds[i].fd);
                    fds[i].fd = -1;
                } else {
                    // 回显数据
                    buffer[bytes_read] = '\0';
                    printf("Echoing to client %d: %s\n", fds[i].fd, buffer);
                    send(fds[i].fd, buffer, bytes_read, 0);
                }
            }
        }
    }

    // 清理资源
    close(server_fd);
    return 0;
}
```

---

### 代码解析

1. **监听和接收连接**：
   - 使用 `poll` 数组的第一个元素监控监听套接字的可读事件。
   - 调用 `accept` 接收新连接，并将客户端套接字加入 `poll` 数组。

2. **动态监控文件描述符**：
   - 通过调整 `pollfd` 数组中的 `fd` 字段动态更新监控的文件描述符集合。
   - 未使用的数组元素设置为 `fd = -1`。

3. **事件处理**：
   - 检查 `revents` 字段判断实际发生的事件。
   - 如果 `POLLIN` 事件触发，读取客户端数据并回显。

---

### 与 `select` 的对比

| 特性                 | `select`                          | `poll`                         |
|----------------------|------------------------------------|---------------------------------|
| 文件描述符限制        | 受 `FD_SETSIZE` 限制              | 无限制（由数组大小决定）         |
| 监控事件类型          | 固定                             | 更灵活                          |
| 性能                 | 随监控文件描述符数量线性增长       | 随监控文件描述符数量线性增长    |
| 动态扩展             | 不方便（需重置集合）              | 简单（调整数组大小即可）         |
| 可用性               | POSIX 标准                       | POSIX 标准                     |

---

### 注意事项

1. **性能问题**：
   - `poll` 的性能随着文件描述符数量线性增长，不适合大规模并发连接。
   - 对于更高效的多路复用，可以使用 `epoll`。

2. **扩展性**：
   - 可通过动态分配和调整 `pollfd` 数组实现更大的监控范围。

3. **事件处理**：
   - 每次 `poll` 返回时，需要遍历整个数组检查事件。

`poll` 是 `select` 的改进版本，虽然它解决了一些 `select` 的缺陷，但在大规模并发场景下仍有局限性，适合中小规模的多路复用需求。