# Socket 编程

## 1.Socket常用函数

1. **socket()**：用于创建一个新的套接字（Socket）。  
   ```c
   int socket(int domain, int type, int protocol);
   ```
   - `domain`：指定协议族，如`AF_INET`（IPv4）、`AF_INET6`（IPv6）。
   - `type`：指定Socket类型，如`SOCK_STREAM`（TCP）、`SOCK_DGRAM`（UDP）。
   - `protocol`：指定协议，通常为`0`，自动选择。

2. **bind()**：将Socket绑定到指定的地址和端口。
   ```c
   int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
   ```
   - `sockfd`：socket()返回的套接字描述符。
   - `addr`：指向`sockaddr`结构的指针，包含IP地址和端口号。
   - `addrlen`：`sockaddr`结构的长度。

3. **listen()**：在指定Socket上监听连接请求（只适用于TCP）。
   ```c
   int listen(int sockfd, int backlog);
   ```
   - `sockfd`：要监听的Socket。
   - `backlog`：等待连接的队列最大长度。

4. **accept()**：接收连接请求并创建一个新Socket来处理该连接。
   ```c
   int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
   ```
   - `sockfd`：监听的Socket。
   - `addr`：指向`sockaddr`结构的指针，存储客户端的地址信息。
   - `addrlen`：存储客户端地址结构的长度。

5. **connect()**：用于客户端连接服务器。
   ```c
   int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
   ```
   - `sockfd`：客户端套接字描述符。
   - `addr`：指向`sockaddr`结构的指针，包含服务器的地址和端口信息。
   - `addrlen`：服务器地址结构的长度。

6. **send()**：用于发送数据。
   ```c
   ssize_t send(int sockfd, const void *buf, size_t len, int flags);
   ```
   - `sockfd`：Socket描述符。
   - `buf`：数据缓冲区指针。
   - `len`：发送数据的长度。
   - `flags`：通常为`0`，也可设置`MSG_DONTWAIT`等标志。

7. **recv()**：用于接收数据。
   ```c
   ssize_t recv(int sockfd, void *buf, size_t len, int flags);
   ```
   - `sockfd`：Socket描述符。
   - `buf`：存储接收数据的缓冲区指针。
   - `len`：缓冲区的大小。
   - `flags`：接收标志，如`MSG_WAITALL`、`MSG_DONTWAIT`等。

8. **sendto()**：用于UDP Socket中发送数据。
   ```c
   ssize_t sendto(int sockfd, const void *buf, size_t len, int flags, const struct sockaddr *dest_addr, socklen_t addrlen);
   ```
   - `dest_addr`：目标地址。

9. **recvfrom()**：用于UDP Socket中接收数据。
   ```c
   ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags, struct sockaddr *src_addr, socklen_t *addrlen);
   ```
   - `src_addr`：存储发送方地址信息。

10. **close()**：关闭Socket。
    ```c
    int close(int sockfd);
    ```
    - `sockfd`：Socket描述符。

11. **shutdown()**：用于部分关闭Socket连接。
    ```c
    int shutdown(int sockfd, int how);
    ```
    - `how`：`SHUT_RD`（停止接收）、`SHUT_WR`（停止发送）、`SHUT_RDWR`（停止接收和发送）。

## 2.udp示例

### 服务器代码示例


### 客户端代码示例


## 3.tcp示例

### 服务器代码示例

### 客户端代码示例
