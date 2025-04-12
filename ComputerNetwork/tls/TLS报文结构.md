# TLS 报文结构

## 资料

- [wireshark下载](https://www.wireshark.org/download.html)
- [数据包下载-tls1.3](./packet/tls1.3.pcapng)
- [数据包下载-tls1.2-ecdhe](./packet/tls1.2-ecdhe.pcapng)
- `packet/tls1.3.pcapng`, `packet/tls1.2-ecdhe.pcapng` 使用wireshark 打开

## 1. 总体结构（TLS 报文是多层嵌套的）

一个 TLS 报文主要包括：

```
[TCP payload]
└─ TLS Record Layer
   └─ TLS Handshake / Application Data / Alert / ChangeCipherSpec
       └─ 实际的握手消息（如 ClientHello、Certificate 等）
```

---

## 2. TLS Record Layer（记录层）

所有 TLS 数据都以 **Record（记录）** 的形式封装：

```c
struct {
    ContentType type;       // 1 byte
    ProtocolVersion version;// 2 bytes
    uint16 length;          // 2 bytes
    opaque fragment[...];   // length 指定的真实数据
} TLSPlaintext;
```

### 字段解释：

| 字段             | 长度     | 说明 |
|------------------|----------|------|
| `ContentType`     | 1 字节   | 类型，如 22（Handshake）、23（Application Data）等 |
| `Version`         | 2 字节   | TLS 版本号，一般写 0x0303（TLS 1.2），TLS 1.3 也写它，为兼容性 |
| `Length`          | 2 字节   | fragment 的长度 |
| `Fragment`        | 可变长度 | 实际载荷：握手消息、加密数据、警告等 |

---

## 3. TLS ContentType 分类

| ContentType | 十六进制 | 含义 |
|-------------|------|------|
| `20`        | 0x14 | ChangeCipherSpec |
| `21`        | 0x15 | Alert（警告） |
| `22`        | 0x16 | Handshake（握手消息） |
| `23`        | 0x17 | Application Data（应用数据） |
| `24`        | 0x18 | Heartbeat（可选） |

---

### 3.1 `ChangeCipherSpec` 类型


`ContentType: ChangeCipherSpec (0x14)` 是 TLS 协议早期版本中的一种记录类型，专门用于通知通信的另一方：**“从现在起我们开始加密了”**。

---

#### 3.1.1 ChangeCipherSpec 报文结构

ChangeCipherSpec 的结构非常简单，仅仅 **1 个字节的固定值**：

| 字节偏移 | 字段名            | 长度 | 值   | 说明            |
|----------|-------------------|------|------|-----------------|
| 0        | `change_cipher_spec` | 1    | `0x01` | 表示“启用加密” |

然后这个 1 字节会被封装进 TLS Record：

| 字段            | 值                 |
|-----------------|--------------------|
| ContentType     | 0x14 (ChangeCipherSpec) |
| ProtocolVersion | TLS 版本（如 0x0303） |
| Length          | 1（仅一个字节）     |
| Payload         | `0x01`              |

---



### 3.2 `Alert` 类型


`ContentType: Alert (0x15)` 是 TLS 协议中用于表示**警告消息**的记录类型。这类消息用于通知对端一些异常或状态变化，比如即将关闭连接、某些错误发生等。

---

#### 3.2.1 Alert 消息结构

在 TLS 中，Alert 报文的结构很简单，**总共只有两个字节**：

| 字节偏移 | 字段名       | 长度 | 含义                  |
|----------|--------------|------|-----------------------|
| 0        | `level`      | 1 字节 | 告警级别（警告 / 致命） |
| 1        | `description`| 1 字节 | 告警的具体类型         |

该结构会被封装在 TLS Record Layer 的 `ContentType = 0x15` 报文中。

---

#### 3.2.2 Alert Level

表示告警的严重程度：

| Level 数值 | 含义     |
|------------|----------|
| `1`        | Warning（警告）|
| `2`        | Fatal（致命错误）|

TLS 1.3 中基本废弃了 Warning，所有 Alert 都视为 fatal（致命）。

---

#### 3.2.3 Alert Description


| 值  | AlertDescription                 | 描述                                                                                                                               |
| --- | -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| 0   | close\_notify                    | 表示发送方希望关闭连接。这是一个正常的关闭通知，通常在连接正常结束时发送。收到此告警后，接收方也应该发送一个 close\_notify 告警并关闭连接。 |
| 10  | unexpected\_message              | 收到了不应该在这个状态下收到的消息。这通常表示协议状态机出现了错误。                                                                   |
| 20  | bad\_record\_mac                 | 收到的记录的 MAC (Message Authentication Code) 校验失败。这表明数据在传输过程中可能被篡改。                                                    |
| 30  | decompression\_failure          | 解压缩收到的数据失败。这可能是由于数据损坏或使用了错误的解压缩算法。                                                                 |
| 40  | handshake\_failure               | 握手过程失败。这是一个通用的错误，可能由多种原因引起，例如协议版本不匹配、密码套件不支持等。                                                     |
| 41  | no\_certificate                 | 服务端请求客户端证书，但客户端没有提供。                                                                                             |
| 42  | bad\_certificate                | 收到的证书损坏或格式不正确。                                                                                                       |
| 43  | unsupported\_certificate        | 收到的证书类型不受支持。                                                                                                         |
| 44  | certificate\_revoked            | 收到的证书已被吊销。                                                                                                             |
| 45  | certificate\_expired            | 收到的证书已过期。                                                                                                             |
| 46  | certificate\_unknown            | 由于某些其他原因，无法验证收到的证书。                                                                                             |
| 47  | illegal\_parameter             | 在握手或其他消息中收到了非法参数。                                                                                                   |
| 48  | unknown\_ca                    | 无法识别证书颁发机构 (CA)。                                                                                                        |
| 49  | access\_denied                 | 拒绝访问请求的资源。                                                                                                           |
| 50  | decode\_error                   | 解码收到的消息失败。                                                                                                             |
| 51  | decrypt\_error                  | 解密收到的消息失败。                                                                                                             |
| 70  | protocol\_version              | 对等方尝试协商一个不受支持的 TLS 协议版本。                                                                                           |
| 71  | insufficient\_security         | 协商的密码套件或密钥长度不够安全。                                                                                                   |
| 80  | internal\_error                | 内部错误，通常表示实现中存在问题。                                                                                                   |
| 90  | user\_canceled                 | 用户取消了操作。                                                                                                               |
| 100 | no\_renegotiation              | 对等方拒绝重新协商连接参数。                                                                                                     |
| 110 | unsupported\_extension         | 收到了不支持的 TLS 扩展。                                                                                                          |
| 111 | certificate\_unobtainable      | 无法获取所需的证书。                                                                                                             |
| 112 | unrecognized\_name             | 收到了无法识别的服务器名称指示 (SNI)。                                                                                              |
| 113 | bad\_certificate\_status\_response | 收到了格式错误的证书状态响应 (例如 OCSP)。                                                                                       |
| 114 | bad\_certificate\_hash\_value  | 证书哈希值校验失败。                                                                                                             |
| 115 | unknown\_psk\_identity         | 收到了未知的预共享密钥 (PSK) 身份。                                                                                                |
| 116 | incorrect\_sni\_extension      | 收到的服务器名称指示 (SNI) 扩展不正确。                                                                                              |
| 117 | unsupported\_ticket            | 收到了不支持的会话票证。                                                                                                         |
| 118 | certificate\_required          | 服务端要求客户端提供证书，但客户端未配置。                                                                                             |
| 120 | handshake\_failure\_alert (TLS 1.3) | TLS 1.3 中更具体的握手失败告警。                                                                                                 |
| 121 | no\_application\_protocol (TLS 1.3) | 客户端和服务端没有共同支持的应用层协议（例如 HTTP/2）。                                                                               |







### 3.3 `Handshake` 类型

TLS 协议中的 **Handshake 报文** 是整个握手过程的核心，它承载了客户端与服务器之间的能力协商、密钥交换、证书验证等关键流程。

---

#### 3.3.1 报文整体结构

TLS 报文最外层是 Record 层，它的 `ContentType` 为 `22 (0x16)` 时，表示这是 **Handshake 报文**。

TLS Record 封装结构：

| 字段           | 长度     | 说明                   |
|----------------|----------|------------------------|
| ContentType     | 1 字节    | 0x16，表示是 Handshake  |
| Version         | 2 字节    | 如 TLS 1.2 为 0x0303    |
| Length          | 2 字节    | 后续 Handshake 的总长度 |
| Handshake Data  | 可变长    | 下面详细讲         |

---

#### 3.3.2 Handshake Data 格式

每条 Handshake 消息都有如下格式：

| 字段         | 长度     | 说明                       |
|--------------|----------|----------------------------|
| HandshakeType | 1 字节    | 指示消息种类，如 ClientHello 是 0x01 |
| Length        | 3 字节    | 后面 body 的长度（24 位大端） |
| Body          | 可变长    | 每种 Handshake 类型定义自己的格式 |

---

#### 3.3.3 常见 HandshakeType 类型

| 类型值 | 名称                | 说明                                  |
|--------|---------------------|---------------------------------------|
| 0x01   | `ClientHello`       | 客户端发起握手请求                     |
| 0x02   | `ServerHello`       | 服务端响应握手请求                     |
| 0x0b   | `Certificate`       | 发送证书链（服务器或客户端）           |
| 0x0c   | `ServerKeyExchange` | TLS 1.2：发送密钥交换参数              |
| 0x0e   | `ServerHelloDone`   | TLS 1.2：表示服务端握手部分完成         |
| 0x10   | `ClientKeyExchange` | 客户端发送密钥交换数据（如 PreMaster） |
| 0x0f   | `CertificateVerify` | 证明证书拥有者持有私钥（签名）         |
| 0x14   | `Finished`          | 握手最后一步，验证握手是否成功         |
| 0x08   | `EncryptedExtensions`| TLS 1.3 新增，通知额外协商参数          |
| 0x1c   | `KeyUpdate`         | TLS 1.3 会话期间更新密钥               |

---

##### `ClientHello`

内容极其丰富，包括：

- 支持的版本：如 `TLS 1.3`
- 随机数：`Random`，用于生成共享密钥
- Session ID：会话恢复
- Cipher Suites：支持的密码套件列表
- Compression Methods：压缩算法（基本已废）
- Extensions：扩展字段，如 SNI、ALPN、supported_groups、key_share 等

---

##### `ServerHello`

字段与 `ClientHello` 类似，但只返回选中的选项：

- 服务器选择的版本、密码套件
- 随机数
- Session ID
- key_share（在 TLS 1.3 中）
- Extensions（确认）

---

##### `Certificate`

- 一个或多个 `CertificateEntry`，按链顺序排列（服务器证书 → 中间证书 → 根证书）

每个证书结构如下：

| 字段      | 长度     | 说明                 |
|-----------|----------|----------------------|
| Length    | 3 字节    | 后续证书长度         |
| Certificate | 可变     | DER 格式的 X.509 证书 |

在 TLS 1.3 中，整个 `Certificate` 是 **加密发送的**。

---

##### `CertificateVerify`

- 使用服务器/客户端的私钥，对之前的 handshake transcript 做签名。
- 结构包括：

| 字段         | 说明                               |
|--------------|------------------------------------|
| SignatureScheme | 如 `rsa_pss_rsae_sha256`         |
| Signature       | 签名值，对 `transcript hash` 签名 |

---

##### `Finished`

- 握手结束标志，发送验证数据，校验对方是否正确协商出密钥。
- 内容：HMAC 校验码，基于 `handshake transcript` 和 `handshake secrets` 计算。

---


### 3.4 `Application Data`


在 TLS 协议中，**Application Data 类型**是最核心的通信数据载体，用来传输用户层（如 HTTP、SMTP、IMAP 等）的实际数据内容。

---

#### 3.4.1 基础结构

TLS 报文的最外层结构是：

```
TLSPlaintext 或 TLSCiphertext
├── ContentType        # 类型字段
├── Version            # 协议版本
├── Length             # 数据长度
├── Fragment           # 实际内容
```

当 `ContentType = 0x17`（即 23），代表该条记录是 **Application Data**。

---

#### 3.4.2 TLS1.2 和 TLS1.3 差别说明

| 项目                | TLS 1.2                                   | TLS 1.3                                         |
|---------------------|-------------------------------------------|------------------------------------------------|
| Application Data 是否加密 |是的，经过对称加密                            | 是的，加密且连 ContentType 都被隐藏                |
| ContentType 位置     | Record Layer 可见（0x17）                 | Record Layer 固定为 0x17，**真正类型被加密**       |
| 包含内容             | 加密的应用层数据（如 HTTP 请求）              | 加密后的 inner content，包括真实类型、数据和 padding |

---

#### 3.4.3 TLS 1.2 Application Data 报文格式

```text
TLSCiphertext
├── ContentType: 0x17
├── Version: 0x0303
├── Length: ...
├── Encrypted Application Data (包含 MAC / padding)
```

解密后是明文数据，例如 HTTP GET 请求。

---

#### 3.4.4 TLS 1.3 Application Data 报文格式

```text
TLSCiphertext
├── ContentType: 0x17         # 始终是 0x17
├── Version: 0x0303
├── Length: ...
├── Encrypted Record:
│     └── InnerPlaintext:
│           ├── Content      # 明文数据（如 HTTP）
│           ├── Padding      # 可变长度填充
│           └── ContentType  # 真实类型（如 handshake, alert）
```

> 注意：**TLS 1.3 把 ContentType 移入加密内容中**，外层看起来所有都是 Application Data（0x17），但实际里面可能是 handshake、alert 等。

---


### 3.5 `Heartbeat`

TLS 中的 **Heartbeat（心跳）扩展**，是一种用于在 TLS 连接上测试是否仍然存活的机制，它是 TLS 的一个可选扩展，由 [RFC 6520](https://www.rfc-editor.org/rfc/rfc6520.html) 定义。


当客户端和服务器之间建立了 TLS 连接后，如果长时间没有数据传输，为了确保连接仍然活着（例如 TCP 层未断但对方宕机了），可以通过 Heartbeat 来检测连接状态。


---

#### 3.5.1 消息结构

Heartbeat 是一种 TLS Record 层的消息，`ContentType = 24 (0x18)`，即：

```
TLSPlaintext
  type: 24 (heartbeat)
  version: 0x0303 (TLS 1.2)
  length: X
  fragment: HeartbeatMessage
```


| 字段名             | 长度     | 说明                                  |
|--------------------|----------|---------------------------------------|
| Type               | 1 字节    | 1 = request，2 = response              |
| Payload Length     | 2 字节    | 后续 payload 的声明长度（大端）        |
| Payload            | 可变     | 任意内容，回显用                      |
| Padding            | ≥16 字节  | 至少 16 字节的填充，防止攻击            |


#### TLS 1.3 完全移除了 Heartbeat 扩展，理由包括：

- 安全性差；
- 使用场景稀少；
- 连接状态检测可由 TCP 层、Keep-Alive 或应用层实现（比如 HTTP ping）。



