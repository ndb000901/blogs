# s_client 命令使用

`openssl s_client` 是一个非常强大的命令行工具，用于调试 TLS/SSL 连接，比如测试服务器的证书、TLS 协议、握手过程、ALPN、SNI、OCSP 等。它几乎是“TLS 层 Wireshark”之外的最佳工具。

---

## 1. 基本语法

```bash

openssl s_client -connect <host:port> [options]
```

例子：

```bash

openssl s_client -connect www.google.com:443
```

---

## 2. 输出内容详解

命令运行后的典型输出：

```text
CONNECTED(00000003)
depth=2 C = US, O = ...
verify return:1
...
---
Certificate chain
 0 s:CN=www.google.com
   i:C=US, O=Google Trust Services LLC, ...
---
Server certificate
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
---
SSL handshake has read XXX bytes and written XXX bytes
Verification: OK
...
New, TLSv1.3, Cipher is TLS_AES_128_GCM_SHA256
...
```

### 分段讲解：

| 区块 | 说明 |
|------|------|
| `CONNECTED(...)` | 表示已连接成功 |
| `Certificate chain` | 服务器证书链 |
| `Server certificate` | 服务器 leaf 证书（PEM 格式） |
| `SSL handshake has read/written` | 握手过程读写的字节数 |
| `Verification: OK` | 证书验证结果 |
| `New, TLSv1.3, Cipher is...` | 使用的 TLS 协议版本和密码套件 |
| `ALPN` | 显示是否协商出 HTTP/2 等 |
| `Start Time`, `Timeout`, `Verify return code` | 时间戳、状态等 |

---

## 3. 常用参数

| 参数 | 说明 |
|------|------|
| `-connect host:port` | 指定要连接的服务 |
| `-tls1_2`, `-tls1_3` | 强制指定 TLS 协议版本 |
| `-cipher <ciphers>` | 指定 Cipher Suites（如 `-cipher 'AES256-GCM-SHA384'`） |
| `-CAfile ca.pem` | 使用指定 CA 验证服务端证书 |
| `-servername <name>` | 指定 SNI（Server Name Indication） |
| `-alpn h2,http/1.1` | 指定 ALPN 协议 |
| `-key client.key` / `-cert client.crt` | 用于客户端双向认证 |
| `-showcerts` | 显示证书链中所有证书 |
| `-msg` | 打印 TLS 消息（低层） |
| `-state` | 打印握手状态 |
| `-debug` | 更详细调试信息 |
| `-ign_eof` | 忽略 EOF，可以用于保持连接看双向数据 |

---

## 4. 示例

###  只看 TLS 协议与 Cipher

```bash

openssl s_client -connect example.com:443
```

### 强制 TLS1.2 + 指定 Cipher

```bash

# openssl ciphers -v 查看加密套件
openssl s_client -connect example.com:443 -tls1_2 -cipher AES256-GCM-SHA384
```

### 检查证书链完整性

```bash

openssl s_client -connect example.com:443 -showcerts
```

### 指定 SNI

```bash

openssl s_client -connect 1.2.3.4:443 -servername example.com
```

### 测试 ALPN

```bash

openssl s_client -connect example.com:443 -alpn h2,http/1.1
```

---



