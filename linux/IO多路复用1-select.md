# IO多路复用1-select

`select` 是 Linux 中一种多路复用 I/O 的系统调用，常用于同时监控多个文件描述符的状态，例如监控多个套接字是否可读、可写或是否发生异常情况。它主要用于构建 I/O 密集型网络服务器或程序。

以下是 `select` 的详细解析：

---

### 函数原型

```c
#include <sys/select.h>

int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

### 参数说明

1. **`nfds`**  
   - 表示待监控的文件描述符的范围，通常为所有文件描述符中最大值加 1。
   - 示例：如果监控的文件描述符是 0、4、5，则 `nfds` 应为 `6`。

2. **`fd_set *readfds`**  
   - 指向 `fd_set` 类型的指针，表示需要监听可读状态的文件描述符集合。
   - 如果不关心可读事件，可传入 `NULL`。

3. **`fd_set *writefds`**  
   - 指向 `fd_set` 类型的指针，表示需要监听可写状态的文件描述符集合。
   - 如果不关心可写事件，可传入 `NULL`。

4. **`fd_set *exceptfds`**  
   - 指向 `fd_set` 类型的指针，表示需要监听异常状态的文件描述符集合。
   - 如果不关心异常事件，可传入 `NULL`。

5. **`struct timeval *timeout`**  
   - 指定超时时间，表示 `select` 等待事件的最长时间：
     - 如果为 `NULL`，表示阻塞等待，直到至少一个文件描述符状态发生变化。
     - 如果设置为 `{0, 0}`，表示立即返回。

---

### 返回值

- **正值**：有状态变化的文件描述符数目。
- **0**：超时返回，没有文件描述符状态发生变化。
- **-1**：调用失败，错误信息存储在 `errno` 中。

---

### 常用宏和函数

1. **`FD_SET(int fd, fd_set *set)`**  
   将文件描述符 `fd` 添加到集合 `set` 中。

2. **`FD_CLR(int fd, fd_set *set)`**  
   从集合 `set` 中移除文件描述符 `fd`。

3. **`FD_ISSET(int fd, fd_set *set)`**  
   检查 `fd` 是否在集合 `set` 中并处于触发状态。

4. **`FD_ZERO(fd_set *set)`**  
   清空集合 `set`。

---

### 使用示例

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <sys/select.h>

#define PORT 10000
#define MAX_CLIENTS 1024
#define BUFFER_SIZE 1024

int main() {
    int server_fd, client_fd, max_fd;
    struct sockaddr_in server_addr, client_addr;
    socklen_t addr_len = sizeof(client_addr);
    fd_set readfds, allfds;
    char buffer[BUFFER_SIZE];
    int client_sockets[MAX_CLIENTS];
    int i;

    // 初始化客户端套接字数组
    for (i = 0; i < MAX_CLIENTS; i++) {
        client_sockets[i] = -1;
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

    // 初始化文件描述符集合
    FD_ZERO(&allfds);
    FD_SET(server_fd, &allfds);
    max_fd = server_fd;

    while (1) {
        readfds = allfds;  // 每次都需要重新复制集合

        // 调用 select
        int activity = select(max_fd + 1, &readfds, NULL, NULL, NULL);
        if (activity < 0) {
            perror("select");
            break;
        }

        // 检查监听套接字是否有新连接
        if (FD_ISSET(server_fd, &readfds)) {
            client_fd = accept(server_fd, (struct sockaddr *)&client_addr, &addr_len);
            if (client_fd < 0) {
                perror("accept");
                continue;
            }

            printf("New connection: socket fd is %d, IP is %s, port is %d\n",
                   client_fd,
                   inet_ntoa(client_addr.sin_addr),
                   ntohs(client_addr.sin_port));

            // 添加到客户端数组和文件描述符集合
            for (i = 0; i < MAX_CLIENTS; i++) {
                if (client_sockets[i] == -1) {
                    client_sockets[i] = client_fd;
                    FD_SET(client_fd, &allfds);
                    if (client_fd > max_fd) {
                        max_fd = client_fd;
                    }
                    break;
                }
            }
        }

        // 检查所有客户端套接字
        for (i = 0; i < MAX_CLIENTS; i++) {
            int sock = client_sockets[i];
            if (sock > 0 && FD_ISSET(sock, &readfds)) {
                int bytes_read = read(sock, buffer, BUFFER_SIZE - 1);
                if (bytes_read <= 0) {
                    // 客户端断开连接
                    printf("Client disconnected: socket fd is %d\n", sock);
                    close(sock);
                    FD_CLR(sock, &allfds);
                    client_sockets[i] = -1;
                } else {
                    // 回显客户端数据
                    buffer[bytes_read] = '\0';
                    printf("Echoing to client %d: %s\n", sock, buffer);
                    send(sock, buffer, bytes_read, 0);
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

### 特性与注意事项

1. **性能瓶颈**  
   - `select` 在处理大量文件描述符时性能较差，因为需要遍历整个文件描述符集合。
   - `poll` 和 `epoll` 是更高效的替代方案。

2. **文件描述符限制**  
   - 受 `FD_SETSIZE` 限制，通常为 1024。如果需要支持更多描述符，需要使用 `poll` 或 `epoll`。

3. **修改后状态**  
   - 调用 `select` 后，文件描述符集合会被内核修改，只保留有事件发生的文件描述符。
   - 如果需要重复使用集合，需每次重新初始化。

---

### 应用场景

- 简单网络服务器的多路复用管理。
- 文件、管道、套接字的事件监听。
- 需要跨平台支持的场景（`select` 通常有更好的兼容性）。

---
