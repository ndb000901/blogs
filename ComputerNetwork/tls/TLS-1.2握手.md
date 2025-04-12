# TLS 1.2 握手

## 1. `TLS 1.2` 握手过程(ECDHE 密钥交换)

Cipher Suite: `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`

| 部分 | 含义 |
|------|------|
| `ECDHE` | 使用椭圆曲线 Diffie-Hellman Ephemeral 密钥交换算法（具备前向保密性）|
| `RSA` | 服务器身份认证使用 RSA 签名证书 |
| `AES_256_GCM` | 对称加密算法使用 256 位密钥的 AES-GCM 模式（认证加密）|
| `SHA384` | PRF（伪随机函数）和 HMAC 使用 SHA-384 |

### 1.1 资料

- [wireshark下载](https://www.wireshark.org/download.html)
- [数据包下载](./packet/tls1.2-ecdhe.pcapng)
- `packet/tls1.2-ecdhe.pcapng` 使用wireshark 打开

### 1.2 握手全过程（Full Handshake）

```text

    客户端                                           服务器
    ────────                                       ─────────

    ClientHello  ────────────────────────────────▶
        - 支持的TLS版本 (e.g. TLS 1.2)
        - 支持的Cipher Suites
        - 支持的椭圆曲线
        - client_random
        - 扩展字段 (如SNI)

                                                  ServerHello
                                  ◀────────────────────────────
                                                      - 选定Cipher Suite
                                                      - server_random
                                                      - 选定椭圆曲线

                                                  Certificate
                                  ◀────────────────────────────
                                                      - 包含RSA公钥的X.509证书

                                                  ServerKeyExchange
                                  ◀────────────────────────────
                                                      - ECDHE参数
                                                      - ephemeral公钥 (EphPubServer)
                                                      - 签名(Sign(EphPubServer))

                                                  ServerHelloDone
                                  ◀────────────────────────────

    ClientKeyExchange ───────────────────────────▶
        - ephemeral公钥 (EphPubClient)

    [计算 pre_master_secret = ECDH(EphPrivClient, EphPubServer)]
    [计算 master_secret = PRF(pre_master_secret, ...)]
    [派生密钥和IV]

    ChangeCipherSpec ────────────────────────────▶
        - 客户端开始使用对称加密

    Finished (已加密) ───────────────────────────▶
        - 校验所有握手消息摘要

                                                  ChangeCipherSpec
                                  ◀────────────────────────────
                                                      - 服务器开始使用对称加密

                                                  Finished (已加密)
                                  ◀────────────────────────────
                                                      - 校验所有握手消息摘要

                          ==== 握手完成，开始安全通信 ====

    应用数据 (加密) ───────────────────────────▶
                                                  应用数据 (加密)
                                  ◀────────────────────────────

```

---

#### 1.2.1 ClientHello（客户端发起握手）

客户端发送：

- 支持的 TLS 版本（如 TLS 1.2）
- 支持的 Cipher Suites（包括 ECDHE_RSA_AES_256_GCM_SHA384）
- 支持的椭圆曲线（如 secp256r1, x25519）
- 支持的压缩方式（通常是 null）
- 随机数 `client_random`（32字节）
- 扩展信息（如 SNI、SessionTicket）

---

#### 1.2.2 ServerHello（服务器回应）

服务器选择：

- 使用的 TLS 版本（TLS 1.2）
- 协议套件：`TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- 服务器随机数 `server_random`
- 选择的椭圆曲线（如 secp256r1）

---

#### 1.2.3 Server Certificate（服务器证书）

服务器发送证书链：

- 使用 **RSA 公钥** 的证书，用于身份认证（签名）
- 通常是 PEM/DER 格式的 X.509 证书链

---

#### 1.2.4 ServerKeyExchange（服务器密钥交换参数）

这一步是 **ECDHE 的关键步骤**，服务器发送：

- ECDHE 的椭圆曲线参数（curve name）
- 服务端的 ephemeral 公钥 `EphPubServer`
- 对上面内容的 **RSA 签名**：证明这些是服务器生成的，防止中间人攻击

签名内容是：
```plaintext
    Signature = Sign( server_private_key, Hash(client_random + server_random + params) )
```

---

#### 1.2.5 ServerHelloDone

服务端表示完成握手初始阶段。

---

#### 1.2.6 ClientKeyExchange（客户端密钥交换）

客户端：

- 生成 ephemeral 公钥 `EphPubClient`
- 发送给服务器

---

#### 1.2.7 协商出共享密钥

此时，客户端和服务端都有对方的 ephemeral 公钥：

- 使用 ECDH 协议计算出共享的 `pre_master_secret`：
```plaintext
    pre_master_secret = ECDH(EphPrivClient, EphPubServer)
```

---

#### 1.2.8 PRF 派生主密钥（master secret）

双方根据：
- `client_random`
- `server_random`
- `pre_master_secret`

通过 PRF（使用 SHA-384）生成：
```plaintext
    master_secret = PRF_SHA384(pre_master_secret, "master secret", client_random + server_random)
```

然后派生出对称加密使用的 key block：

```plaintext
    key_block = PRF_SHA384(master_secret, "key expansion", server_random + client_random)
```

该 key block 会被切成：
- 客户端加密密钥（256 bit）
- 服务端加密密钥（256 bit）
- MAC 密钥（GCM 下不再单独使用）
- IV 初始值（有时内嵌）

---

#### 1.2.9 ChangeCipherSpec（客户端 → 服务器）

客户端通知服务器：**之后的报文将开始使用加密通信。**

---

#### 1.2.10 Finished（客户端加密消息）

客户端发送一条已加密的 Finished 消息，包含握手所有消息的摘要，防止中间人攻击。使用的摘要算法为 SHA-384。

---

#### 1.2.11 ChangeCipherSpec（服务器 → 客户端）

服务器也切换到加密状态。

---

#### 1.2.12 Finished（服务器加密消息）

服务器同样发送加密的 Finished 消息，包含所有握手内容摘要。

---

#### 1.2.13 握手完成，进入加密通信阶段

双方现在拥有：
- 相同的对称密钥（256 位 AES）
- 使用 GCM 模式进行加密和认证（AEAD）
- 使用随机 IV 和 Sequence Number 保证数据不可重放

---



#### 握手关键数据流示意图（简化）

```plaintext
ClientHello           → client_random, supported suites
ServerHello           ← server_random, selected suite
Certificate           ← RSA证书
ServerKeyExchange     ← ECDHE 公钥 + 签名
ServerHelloDone       ←
ClientKeyExchange     → ECDHE 公钥
ChangeCipherSpec      → 加密切换
Finished              → 已加密
ChangeCipherSpec      ←
Finished              ← 已加密
```


## 2. `TLS 1.2` 握手过程(RSA 密钥交换)

Cipher Suite: `TLS_RSA_WITH_AES_256_GCM_SHA384 (0x009d)`

| 部分 | 含义 |
|------|------|
| `TLS_RSA` | 密钥交换算法（Key Exchange）与认证算法：RSA |
| `AES_256_GCM` | 对称加密算法：AES 256 位 GCM 模式 |
| `SHA384` | 消息认证码（MAC）和伪随机函数（PRF）中的哈希函数 |

- **RSA**：用于传输对称密钥（无前向保密）
- **AES-256-GCM**：用于加密通信内容（GCM 是 AEAD 模式，支持同时加密+认证）
- **SHA384**：用于 TLS PRF 派生密钥和 GCM 的 AAD 认证

---

### 资料

- [wireshark下载](https://www.wireshark.org/download.html)
- [数据包下载](./packet/tls1.2-rsa.pcapng)
- `packet/tls1.2-rsa.pcapng` 使用wireshark 打开

### 2.1 TLS 1.2 握手过程（使用 TLS_RSA_WITH_AES_256_GCM_SHA384）

```text

客户端                                           服务器
  |                                                 |
  | ----------- ClientHello ----------------------> |
  |      - 支持的TLS版本（如 TLS 1.2）                 |
  |      - 支持的Cipher Suites                       |
  |      - ClientRandom                             |
  |      - 扩展（如SNI等）                            |
  |                                                 |
  | <----------- ServerHello ---------------------- |
  |       - 选中的TLS版本、Cipher Suite               |
  |       - ServerRandom                            |
  |       - 服务器证书（含RSA公钥）                    |
  |                                                 |
  | -------- PreMasterSecret（用RSA加密） ------->    |
  |                                                 |
  |    （服务器用私钥解密出PreMasterSecret）            |
  |                                                 |
  |  <--（双方各自使用PRF计算MasterSecret）------>     |
  |   - PRF输入：PreMasterSecret + Randoms           |
  |   - 结果：对称加密密钥、IV                         |
  |                                                 |
  | ------- ChangeCipherSpec -------------------->  |
  | ------- Finished (使用AES-256-GCM加密) ------>   |
  |                                                 |
  | <------ ChangeCipherSpec ---------------------  |
  | <------ Finished (使用AES-256-GCM加密) -------   |
  |                                                 |
  |           握手完成，进入加密通信                    |
  |                                                 |

```


#### 2.1.1 Client Hello（客户端发起握手）

- 提供：
    - 支持的 TLS 版本（如 TLS 1.2）
    - 支持的 Cipher Suites（含 TLS_RSA_WITH_AES_256_GCM_SHA384）
    - 随机数 `ClientRandom`
    - 支持的扩展（如 SNI、ALPN、OCSP、SessionTicket）

---

#### 2.1.2 Server Hello（服务器响应）

- 确定：
    - 使用的 TLS 版本
    - 选定的 Cipher Suite（此例中为 0x009d）
    - 随机数 `ServerRandom`
- 附带：
    - 服务器的 X.509 RSA 证书（用于客户端验证服务器身份 + 提供公钥）

---

#### 2.1.3 客户端验证证书（Certificate Verify）

- 客户端验证服务器证书的合法性（是否受信任、未过期等）

---

#### 2.1.4 客户端生成 `PreMasterSecret` 并加密传输

- 客户端随机生成一个 48 字节的 PreMasterSecret
- 用服务器的 **RSA 公钥** 加密后发给服务器（此步骤为 RSA 密钥交换核心）
- 这也是该套件不具备“前向保密”的原因——只要服务器私钥泄露，历史流量也会被解密

---

#### 2.1.5 双方基于 `PreMasterSecret`、`ClientRandom` 和 `ServerRandom` 派生出主密钥（MasterSecret）

- 使用 PRF（伪随机函数，底层是 SHA384）派生 `MasterSecret`
- 然后从 `MasterSecret` 派生出：
    - 对称加密密钥
    - 消息认证密钥（MAC）
    - 初始化向量（IV）

---

#### 2.1.6 客户端发送 `ChangeCipherSpec` + Finished 消息

- 表示后续消息将开始加密
- Finished 消息是对握手过程的摘要签名，用于防篡改验证

---

#### 2.1.7 服务器响应 `ChangeCipherSpec` + Finished 消息

- 表示也开始使用新协商好的密钥进行加密通信

---

#### 2.1.8 握手完成，进入应用数据阶段

- 所有后续数据都使用 AES-256-GCM 进行加密
- GCM 是 AEAD 模式，加密 + 完整性校验一步完成（不再需要单独 MAC）

---


