# 密钥派生-KDF

## 1. 密钥派生（Key Derivation）

**密钥派生**就是从一个初始密钥材料（比如：共享密钥、口令、种子）中，**计算出一个或多个高质量密钥**的过程。

形式上可表示为：
```text
KDF(secret, salt, info) → derived_key
```

它通常包含以下目标：
1. **增强熵**：使弱口令变得更难暴力破解（如 PBKDF2）
2. **拉长密钥**：从一个短的密钥派生多个新密钥（如 TLS 中 key_block）
3. **不同用途隔离**：为不同协议组件生成不同用途的密钥（如 encrypt_key, mac_key）

---

## 2. 常见使用场景

| 场景 | 描述 |
|------|------|
| TLS / DTLS | 协商出 `pre-master secret`，通过 KDF 派生出 MAC key、加密 key、IV 等 |
| WebRTC / SRTP | 使用 DTLS 密钥交换结果，再派生 SRTP key |
| Wi-Fi WPA2 | 使用 PBKDF2 从密码派生出 PSK |
| 用户密码存储 | 用 bcrypt/scrypt/argon2 从密码派生 hash |
| JWT / HMAC | 用 HKDF 派生不同功能的子密钥 |
| ECDH / DH | 双方共享一个 shared secret，派生通信密钥 |

---

## 3. 常见的 KDF 算法

### PBKDF2（Password-Based KDF）

- 主要用于从口令中派生密钥，防止暴力破解
- 通常结构：
  ```
  DK = PBKDF2(password, salt, iterations, length)
  ```
- 实现原理：大量迭代 HMAC 函数（如 HMAC-SHA256）

### HKDF（HMAC-based KDF）

- 安全、现代、用途广泛（TLS 1.3、Signal、WebRTC）
- 构造分两步：
  ```text
  PRK = HMAC(salt, IKM)          # 提取阶段
  OKM = HMAC(PRK, info || ctr)   # 扩展阶段
  ```
  其中：
    - `IKM`：输入密钥材料
    - `salt`：可选（一般推荐提供）
    - `info`：上下文信息，用于不同用途的区分
- 特点：支持生成任意长度输出，构造简单、安全性可证明

### Scrypt / Argon2

- 主要用于密码学抗 GPU 和 ASIC 暴力破解的场景
- 比 PBKDF2 更抗并行和内存优化攻击

---


