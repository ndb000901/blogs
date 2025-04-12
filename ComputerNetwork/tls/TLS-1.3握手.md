# TLS 1.3 握手

## 1. TLS1.3 握手(TLS_AES_256_GCM_SHA384)


Cipher Suite: `TLS_AES_256_GCM_SHA384`


| 组成部分 | 含义 |
|----------|------|
| `AES_256_GCM` | 对称加密算法：AES，密钥长度 256 位，GCM 模式（AEAD） |
| `SHA384` | 用作 HKDF（HMAC-based Extract-and-Expand Key Derivation Function）的哈希函数 |
|没有密钥交换算法字段 | 因为在 TLS 1.3 中，密钥交换算法（如 X25519、secp256r1）不再出现在 Cipher Suite 名称里，而是作为扩展 negotiated |


- 默认强制使用 **前向保密（PFS）**
- 所有握手消息都被加密（更安全）
- 握手减少到 **1-RTT**
- 抛弃了旧的 RSA key exchange、静态 DH、CBC、SHA1 等旧机制
- Cipher Suite 只包含 AEAD 算法 + Hash 算法

---

### 资料

- [wireshark下载](https://www.wireshark.org/download.html)
- [数据包下载](./packet/tls1.3.pcapng)
- `packet/tls1.3.pcapng` 使用wireshark 打开


### 1.1 TLS 1.3 握手过程（使用 TLS_AES_256_GCM_SHA384）

文本流程图如下：

```text
客户端                                           服务器
  |                                                 |
  | ----------- ClientHello ----------------------> |
  |                                                 |
  | <--------- ServerHello ------------------------ |
  |             （开始使用 handshake key 加密）        |
  | <------ EncryptedExtensions（加密） ------------ |
  | <------ Certificate（加密） -------------------- |
  | <------ CertificateVerify（加密） -------------- |
  | <------ Finished（加密） ------------------------|
  |                                                 |
  | -------- Finished（加密） -------------------->  |
  |                                                 |
  |         双方握手完成，进入应用数据加密通信阶段        |

```

---

#### 1.1.1 ClientHello
- 发送支持的 Cipher Suites（如 0x1302）
- 包含 `KeyShare` 扩展，内含客户端的 DH/ECDH 公钥（如 X25519）
- 发送 `supported_versions` 扩展，表明支持 TLS 1.3

#### 1.1.2 ServerHello
- 选定 Cipher Suite 和密钥交换参数（KeyShare）
- 与客户端的 KeyShare 协商出共享密钥（ECDHE）
- 从此之后所有数据都使用 `early_secret` 派生出的 key 加密

#### 1.1.3 Server 加密发送：
- `EncryptedExtensions`：发送额外参数（如 ALPN、SNI）
- `Certificate`：服务器证书链（加密）
- `CertificateVerify`：服务器用私钥签名 handshake transcript(握手摘要)
- `Finished`：包括完整性校验，表明握手完成

#### 1.1.4 Client 加密发送：
- `Finished`：确认 handshake transcript 验证无误

---



