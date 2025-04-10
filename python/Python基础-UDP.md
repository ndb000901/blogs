# Python基础-UDP

## 1 **创建 UDP 套接字**

创建一个 UDP 套接字需要指定协议类型为 `SOCK_DGRAM`：
```python
import socket

# 创建一个 UDP 套接字
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
```

## 2 **发送数据**

通过 `sendto()` 方法可以将数据发送到目标 IP 地址和端口：
```python
server_address = ('localhost', 12345)
message = b'This is a test message'

# 发送数据
sock.sendto(message, server_address)
```

## 3 **接收数据**

通过 `recvfrom()` 方法接收来自任意客户端的数据：
```python
data, address = sock.recvfrom(4096)  # 接收最多 4096 字节的数据
print(f"Received {data} from {address}")
```

## 4. **UDP 服务器示例**

一个简单的 UDP 服务器，它监听指定端口并接收来自客户端的数据：
```python
import socket

# 创建 UDP 套接字
server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server_socket.bind(('localhost', 12345))

print("Waiting for messages...")
while True:
    data, client_address = server_socket.recvfrom(4096)
    print(f"Received message: {data} from {client_address}")
    server_socket.sendto(b'ACK', client_address)  # 回复确认信息
```

## 5 **UDP 客户端示例**

一个简单的 UDP 客户端，发送消息到指定的 UDP 服务器：
```python
import socket

# 创建 UDP 套接字
client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

server_address = ('localhost', 12345)
message = b'Hello, UDP server!'

# 发送数据
client_socket.sendto(message, server_address)

# 接收响应
data, server = client_socket.recvfrom(4096)
print(f"Received response: {data}")

client_socket.close()
```

---
