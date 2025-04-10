# Python基础-TCP

## 1. **创建 TCP 套接字**

```python
import socket

# 创建 TCP 套接字
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

- `socket.AF_INET`：指定 IPv4 地址族。
- `socket.SOCK_STREAM`：指定 TCP 协议。

## 2. **TCP 服务器实现**

TCP 服务器一般通过以下步骤实现：
1. 创建一个 TCP 套接字。
2. 绑定到指定的 IP 地址和端口。
3. 开始监听来自客户端的连接。
4. 接收并处理客户端发送的数据。
5. 关闭连接。

## 3. **TCP 服务器代码示例**

```python
import socket

# 创建 TCP 套接字
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 绑定到指定的 IP 地址和端口
server_socket.bind(('localhost', 12345))

# 开始监听客户端的连接请求，最大连接数为 5
server_socket.listen(5)
print("Waiting for a connection...")

# 接受连接并返回一个客户端连接对象和客户端的地址
client_socket, client_address = server_socket.accept()
print(f"Connection established with {client_address}")

# 接收数据
data = client_socket.recv(1024)
print(f"Received data: {data.decode()}")

# 发送数据
client_socket.send(b'Hello, Client!')

# 关闭连接
client_socket.close()
server_socket.close()
```

- `bind()`：将服务器的套接字绑定到特定的 IP 地址和端口。
- `listen()`：监听传入的连接请求。`listen()` 需要一个参数，表示最大连接数。
- `accept()`：接受一个连接请求，返回一个新的套接字对象和客户端地址。


## 4. **TCP 客户端实现**

```python
import socket

# 创建 TCP 套接字
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 连接到服务器
client_socket.connect(('localhost', 12345))

# 发送数据
client_socket.send(b'Hello, Server!')

# 接收数据
data = client_socket.recv(1024)
print(f"Received from server: {data.decode()}")

# 关闭连接
client_socket.close()
```

TCP 传输数据时，需要使用 `send()` 方法发送数据，使用 `recv()` 方法接收数据。可以指定接收缓冲区的大小来控制一次接收多少字节的数据。

---


