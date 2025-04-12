# SRTCP



**SRTCP** 是 **RTCP 的加密版本**，全称是：

> **Secure Real-time Transport Control Protocol**

它是配合 SRTP（Secure RTP）一起使用的，提供 RTCP 消息的 **机密性、完整性、重放保护**。

## 1. 设计目标与功能：
- 加密 RTCP 内容（可选）
- 验证 RTCP 包的完整性和来源
- 防止重放攻击（Replay Protection）

---

## 2. 为何要保护 RTCP

虽然 RTP 传输媒体流（必须加密），RTCP 只是控制信息，但它也很敏感，举例：

- RTCP PLI/FIR 可控制视频关键帧请求，若被篡改将破坏媒体流；
- RTCP REMB/TWCC 用于带宽估计，攻击者可用来误导码率调节；
- RTCP BYE 表示终止流，攻击者可使用户被强制“踢出”。

因此，在安全敏感场景中，**SRTCP 是强制性的补充方案**。

---

## 3. SRTCP 报文结构

SRTCP 使用如下结构来扩展原始 RTCP 报文：

```
+---------------------------+
|   RTCP Payload (原始 RTCP 包) |
+---------------------------+
|  SRTCP Index (32 bits)    |
+---------------------------+
|  Authentication Tag       |
|  (MAC, e.g., 80 or 160 bits) |
+---------------------------+
```

### 字段说明：

| 字段 | 描述 |
|------|------|
| **RTCP Payload** | 原始的未加密或加密的 RTCP 消息 |
| **SRTCP Index** | 序列号，防重放。包含一个 E（Encryption）位 |
| **Authentication Tag** | 消息认证码（MAC），验证完整性、身份、防篡改 |

---

### SRTCP Index（32 bits）

```
  0               1               2               3
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |E|                      SRTCP Packet Index                     |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

| 位 | 含义 |
|----|------|
| **E bit (1)** | 表示 RTCP Payload 是否被加密（1=加密） |
| **Index (31)** | 单调递增序列号，防重放攻击 |

---

## 4. SRTCP 与 SRTP 对比

| 特性 | SRTP | SRTCP |
|------|------|--------|
| 协议层 | RTP | RTCP |
| 加密内容 | 媒体帧 | 控制报文 |
| 必要性 | 必须加密 | 加密可选，认证必须 |
| Index | 48-bit SRTP Index | 32-bit SRTCP Index |
| E bit | 无 | 有（加密标志） |
| 用途 | 保护语音/视频 | 保护控制信息，如 BYE/PLI/FIR 等 |
| 加密算法 | AES-CTR | AES-CTR |
| 认证算法 | HMAC-SHA1 | HMAC-SHA1 |

---

## 5. WebRTC 中的 SRTCP

在 WebRTC 中：

- RTP 使用 **SRTP**
- RTCP 使用 **SRTCP**
- 两者密钥通过 **DTLS-SRTP 协议协商**
- 通信初始化后，所有媒体数据和控制信息都走 SRTP/SRTCP

SRTCP 报文也经过 DTLS 握手后协商出的密钥加密和认证。

---

