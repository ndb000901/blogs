# SRTP

**SRTP** 是 RTP 的加密扩展，全称是 **Secure Real-time Transport Protocol**，定义于 [RFC 3711](https://www.rfc-editor.org/rfc/rfc3711.html)。

> 它的目标是：在 RTP 协议之上提供**机密性、消息完整性、认证和重放保护**。

它适用于音视频通话、WebRTC、SIP 等实时通信中，尤其在公网传输环境中是标配。

---

## 1. SRTP 提供的安全性功能

| 功能     | 说明 |
|----------|------|
| 机密性    | 加密 RTP 的负载部分（payload） |
| 完整性校验  | 使用 HMAC 校验消息是否被篡改 |
| 认证     | 确保数据来自可信端 |
| 重放保护 | 防止攻击者捕获并重放旧的 RTP 包 |

---

## 2. SRTP 协议结构

SRTP 数据包的结构其实是「RTP 包头 + 加密负载 + HMAC 校验值」。

### 原始 RTP 包结构：
```
RTP Header (固定 12 字节)
Payload（音频或视频）
```

### SRTP 包结构：
```
RTP Header（原始 RTP 包头，不加密）
Encrypted Payload（加密的音视频数据）
Authentication Tag（完整性校验值，默认 10 字节）
```

> RTP Header 是明文传输的，因为接收端需要它来解复用、解码、同步等。

---

## 3. SRTP 加密机制

### 加密算法

| 类型      | 算法                     | 用途         |
|-----------|--------------------------|--------------|
| 加密      | AES-CTR 模式             | 加密 payload |
| 认证      | HMAC-SHA1-80（默认）     | 完整性校验   |
| 密钥派生  | 使用 `master key + salt` | 生成子密钥   |

> 可选算法还有 AES-GCM（Google WebRTC 默认推荐用 GCM 模式）

---

## 4. 密钥协商方式（与 DTLS 结合）

SRTP 自身不包含密钥协商功能，常通过以下方式协商密钥：

### **DTLS-SRTP（WebRTC 默认）**
- 使用 DTLS（TLS over UDP）协商密钥
- 再派生出 SRTP 的主密钥和 salt
- 安全、灵活，是现代 WebRTC 标准

### **SDES（Session Description Protocol Security Descriptions）**
- 直接通过 SDP 信令交换 key（明文或加密）
- 老旧方式，不推荐

---

## 5. Replay Protection（重放保护）

SRTP 使用 **64-bit 滑动窗口**机制来识别并丢弃重复的 RTP 包：

- 每个 RTP 包有序号 `seq`（16 位）
- 通过计算相对位置与窗口中是否已记录来判断是否为重放攻击

---


## 6. WebRTC 中 SRTP 的工作流简要图

```text
[DTLS握手完成]
       ↓
[提取密钥]
       ↓
[派生 SRTP 密钥 + salt]
       ↓
[开始 SRTP 加密/解密]
```

---



