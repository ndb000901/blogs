# SCTP协议

**SCTP** 全称为 **Stream Control Transmission Protocol**，**流控制传输协议**，是一个位于传输层的协议，最初用于信令系统（SS7）在 IP 网络上传输。

> 它结合了 TCP 的可靠性和 UDP 的多流、无头阻塞等优点。

---

## 1. SCTP 与 TCP/UDP 对比

| 特性             | TCP      | UDP  | SCTP                 |
|------------------|----------|------|----------------------|
| 连接控制         | 有连接      | 无连接  | 有连接                  |
| 有序传输         | 支持       | 不支持  | 支持每个子流内部有序        |
| 多流（Multi-streaming） | 不支持       | 不支持    |  避免 head-of-line 阻塞 |
| 多宿主支持（Multi-homing） | 不支持 | 不支持    |  网络容错更强             |
| 消息边界保留     |  字节流     | 支持     |  message-oriented    |
| 传输可靠性       | 支持         | 不支持     | 支持                     |
| 拥塞控制         | 支持         | 不支持     | 支持                     |

---

## 2. SCTP 的关键特性

### 2.1 **多流（Multi-streaming）**

- 单一连接（association）中有多个 stream。
- 每个 stream 可以独立有序传输数据。
- 避免了 TCP 的 Head-of-Line Blocking 问题。

### 2.2 **多宿主（Multi-homing）**

- 支持单个端点拥有多个 IP。
- 可用作 failover，提高连接可靠性。

### 2.3 **消息导向（Message-oriented）**

- 每个 SCTP 数据单元都是消息（不像 TCP 是字节流）。
- 支持消息大小最大可达 64KB。

### 2.4 **传输控制**

- 拥塞控制 + 流量控制。
- 支持快速重传、SACK、路径选择等。

---

## 3. SCTP 协议结构

### 3.1 SCTP Header（通用头部）
```
0                   1                   2                   3  
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|    Source Port Number        |    Destination Port Number     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      Verification Tag                         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           Checksum                            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

- **Source/Destination Port**：端口号
- **Verification Tag**：校验用的 tag，防止伪连接攻击
- **Checksum**：校验整个报文（CRC32C）

### 3.2 Chunk 格式（数据单位）

每个 SCTP 包由多个 **Chunk** 组成（Header + Payload），比如：

- INIT Chunk：建立连接时使用
- DATA Chunk：传输数据
- SACK Chunk：确认（类似 TCP ACK）
- HEARTBEAT：保活检测

---

## 4. SCTP 协议状态机（简略）

```
INIT -----> INIT_ACK
   |            ↓
 COOKIE_ECHO -> COOKIE_ACK
   |            ↓
ESTABLISHED（数据传输阶段）
```

> SCTP 的握手是四次：INIT -> INIT_ACK -> COOKIE_ECHO -> COOKIE_ACK

---

## 5. WebRTC 中的 SCTP 用法

WebRTC 中用于 **DataChannel（数据通道）** 的底层传输就是 **SCTP over DTLS over UDP**。

```text
WebRTC DataChannel
       ↓
    SCTP (多流可靠传输)
       ↓
    DTLS (加密)
       ↓
     UDP (传输)
```

### WebRTC 的 SCTP 特点：
- 使用一个 SCTP association 承载多个 DataChannel（每个为一个 stream）
- 支持有序/无序、可靠/不可靠发送（Partial Reliability）
- 通过 SDP `a=sctp-port:` 协商

---



