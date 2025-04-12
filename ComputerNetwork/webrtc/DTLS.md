# DTLS 

## 1. DTLS

**DTLS**，全称 **Datagram Transport Layer Security**，即**数据报传输层安全协议**，是 TLS（传输层安全协议）在 **UDP 等无连接协议**上的扩展版本。

> 目标：为 **UDP** 提供类似 TLS 的安全保障（加密、身份认证、消息完整性）。

主要用于：

- **WebRTC**
- **CoAP（IoT）**
- **SIP over UDP**
- **QUIC（部分场景）**

---

## 2. DTLS vs TLS 核心对比

| 特性             | TLS (基于 TCP) | DTLS (基于 UDP) |
|------------------|----------------|------------------|
| 传输方式         | 面向连接         | 无连接             |
| 顺序保证         | 内建（TCP）      | 自行处理重排、丢包 |
| 重传机制         | 不需要（TCP 负责） | 需要自行实现        |
| 拥塞控制         | TCP 控制        | 没有               |
| 用途             | HTTPS、SMTP     | WebRTC、VoIP       |

---

## 3. DTLS 协议栈结构

```
 应用层（SRTP/SCTP）
     ↓
 DTLS（加密）
     ↓
 UDP（传输）
```

在 WebRTC 中常见结构：

```
SCTP over DTLS over UDP
```

> 注意：DTLS 本身不会做媒体编解码、转发，它的职责是 **加密 UDP 流量，提供安全保障**。

---

## 4. DTLS 核心功能

| 能力       | 说明                                           |
|------------|------------------------------------------------|
| 加密       | 所有传输内容被加密，防止窃听                        |
| 身份认证   | 可验证对端证书或 fingerprint                      |
| 数据完整性 | 提供消息摘要（HMAC），防止被篡改                   |

---

## 5. DTLS 握手流程

DTLS 基于 TLS 1.2（或 1.3）设计，但需要处理无连接性，以下为典型 **DTLS 1.2 握手流程**：

```
Client                           Server
  |-------> ClientHello ------------->|
  |<------ HelloVerifyRequest --------|   // 防止 DoS
  |-------> ClientHello (with cookie) |
  |<-------- ServerHello -------------|
  |<-------- Certificate -------------|
  |<-------- ServerKeyExchange -------|
  |<-------- ServerHelloDone ---------|
  |--------> Certificate (optional)   |
  |--------> ClientKeyExchange        |
  |--------> CertificateVerify        |
  |--------> ChangeCipherSpec         |
  |--------> Finished                 |
  |<-------- ChangeCipherSpec         |
  |<-------- Finished                 |
```

### 特点：
- 引入了 `HelloVerifyRequest` + Cookie 验证 → 抵抗 UDP 伪造攻击
- 握手数据可丢失，DTLS 实现需做：
    - 重传
    - 超时定时器
    - 消息编号处理（epoch, sequence number）

---

## 6. DTLS 报文格式

每个 DTLS 包头格式如下：

```
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Content Type |Version|        Epoch        |  Sequence Number |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   Length    |                 Fragment                  |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    DTLS Record Payload                  |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

- **Content Type**：如 handshake (22), application_data (23)
- **Epoch**：密钥切换次数（类似 TLS session）
- **Sequence Number**：防止重放攻击
- **Fragment**：分片字段，DTLS 支持应用层消息分片

---

## 7. WebRTC 中的 DTLS 实际用途

在 WebRTC 中，DTLS 主要用于 **DataChannel + 媒体加密密钥协商（SRTP）**：

```text
Signaling 信令层协商 → 提供 DTLS fingerprint (SHA-256)

Peer A (客户端)
  |
  | <---- UDP Candidate 配对后 ---->
  |
  | === DTLS 握手开始 ===>
  |      握手成功后：
  |        1. 协商出 SRTP 密钥
  |        2. 开始加密音视频流 (SRTP)
  |        3. SCTP 数据通道建立 (DataChannel)
```

SDP 中关键字段：

```
a=setup:actpass
a=fingerprint:sha-256 <hash值>
```

---

## 8. DTLS 和 SRTP 的关系

DTLS 并不直接传输媒体，而是用于协商 SRTP 的加密密钥。

- 协议名：**DTLS-SRTP**
- 用 DTLS 握手协商出密钥（client/server keying material）
- 然后用这些 key 启动 SRTP 加密媒体流

> 所以 SRTP 是真正传音视频，DTLS 是为 SRTP 提供密钥



