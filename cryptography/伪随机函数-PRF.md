# 伪随机函数-PRF

**伪随机函数（PRF，Pseudorandom Function）**，它是密码学中非常基础且关键的概念，在 WebRTC、TLS、JWT、MAC、加密通信等场景中都会遇到。

---

## 1. 伪随机函数（PRF）

**定义：**
> 伪随机函数（PRF）是一个不可区分于真正随机函数的**确定性函数**，它接受一个密钥和输入，输出看起来像“随机数”的结果。

形式表示：
```
PRF(key, input) → output
```

满足两个条件：
1. **确定性**：给定同一个 `key` 和 `input`，结果始终相同。
2. **伪随机性**：结果对没有密钥的人来说，看起来像随机的，不可预测。

---

## 2. 常见的 PRF 实现方式

在实际系统中，常用的 PRF 构建方式是：

### 基于 HMAC 构建（最常见）
```text
PRF(key, input) = HMAC(key, input)
```
- HMAC 是一个非常强的伪随机函数，安全性由底层哈希算法（如 SHA-256）保障
- 用于：TLS 1.2、IPSec、JWT、SRTP、WebRTC DTLS、TURN 鉴权等

---

### 基于 Block Cipher（分组加密）构建
```text
PRF(key, input) = AES(key, input)
```
- 将对称加密算法（如 AES）视为一个 PRF
- 比如 TLS 1.3 中，HKDF 会用到 `AES-CTR` 模式配合扩展输出长度

---

### 基于 KDF（Key Derivation Function）构建
```text
PRF = KDF(key, context) → output
```
- 比如：TLS 1.3 使用的 HKDF（HMAC-based KDF）本身就是一个 PRF 构造
- 多用于派生 session key、salt、IV 等参数

---
