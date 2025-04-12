# TURN 协议

TURN（Traversal Using Relays around NAT）是 STUN 协议的扩展，主要用于在 **严格 NAT 或 Symmetric NAT 无法打洞成功的情况下**，通过中继服务器转发媒体数据，从而建立 WebRTC 或其他 P2P 连接。

它解决了 STUN 的“打洞失败”问题，是 WebRTC 架构中 ICE 协议的重要组成部分。

---

## 1. TURN 的核心概念

| 概念                  | 说明 |
|-----------------------|------|
| **Client（客户端）**   | 需要穿透 NAT 的终端，比如浏览器、移动端等。 |
| **TURN Server（中继服务器）** | 运行 TURN 协议的服务器，帮客户端中继数据。 |
| **Peer（对端）**        | 与客户端通信的另一方，比如另一个浏览器。 |
| **Relay Address（中继地址）** | TURN Server 为客户端分配的公网 IP 和端口，Peer 可以向其发送数据。 |

---


## 2. TURN 协议工作流程

### 2.1 客户端向 TURN Server **Allocate** 一个中继地址

- 发出 `Allocate Request`。
- TURN Server 返回一个可供 Peer 使用的中继地址。

### 2.2 客户端发送 `CreatePermission` 请求

- 用于授权 Peer 与客户端通信，避免任意主机向 TURN Server 发数据。

### 3.3 可选：客户端发送 `ChannelBind`

- 绑定一个通道（Channel Number），后续可以用更紧凑的数据格式传输数据，提高效率。

### 3.4 数据中继过程

- 客户端把数据发送到 TURN Server，TURN Server 转发给 Peer。
- Peer 把数据发送到 TURN Server，TURN Server 再转发给客户端。

---

## 3. TURN 消息类型（Method）

与 STUN 类似，但方法不同：

| 方法名              | 说明                                |
|---------------------|-------------------------------------|
| `Allocate`          | 申请中继地址                         |
| `Refresh`           | 保活中继地址                         |
| `CreatePermission`  | 授权某个 IP 向中继地址发送数据       |
| `Send`              | 客户端通过 TURN server 向 Peer 发送数据 |
| `Data`              | Peer 通过 TURN server 向客户端发送数据 |
| `ChannelBind`       | 建立 Channel 绑定，提高传输效率     |

---

## 4. TURN 常见属性（Attributes）

| 属性类型                  | 描述 |
|---------------------------|------|
| `USERNAME`                | TURN 身份认证的用户名 |
| `REALM`                   | 身份域，用于 long-term credential |
| `NONCE`                   | 防止重放攻击的随机串 |
| `MESSAGE-INTEGRITY`       | HMAC-SHA1 校验值 |
| `XOR-RELAYED-ADDRESS`     | TURN 中继地址（服务器分配）|
| `XOR-MAPPED-ADDRESS`      | 客户端在服务器看到的地址 |
| `REQUESTED-TRANSPORT`     | 通常为 UDP（17） |
| `LIFETIME`                | 分配的资源生存时间（秒）|
| `DATA`                    | 携带的实际媒体数据 |
| `CHANNEL-NUMBER`          | 与对端绑定的通道编号 |

---


## 5. TURN 工作流程概览

1. **Allocate Request**：客户端向 TURN 服务器申请一个中继地址（relayed address）。
2. **Permission**：客户端为要通信的对端 IP 建立 permission。
3. **Channel Bind（可选）**：为对端绑定一个 Channel，优化数据发送。
4. **Send / Data**：客户端将数据发送给 TURN，TURN 再转发给对端。
5. **Refresh**：定期续期分配的中继资源（如 relayed address）。



