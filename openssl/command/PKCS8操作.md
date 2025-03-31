# PKCS8操作

`openssl pkcs8` 命令用于处理 PKCS#8 格式的私钥，包括加密、解密和转换等操作。PKCS#8 是一种标准的私钥格式，支持密码保护，并在各种加密标准和应用中广泛使用。

---

## 1. PKCS#8 介绍
PKCS#8 定义了一种标准格式来存储私钥，主要有两种编码方式：
- **PEM 格式（Base64 编码，文本格式）**：
  - 以 `-----BEGIN ENCRYPTED PRIVATE KEY-----` 开头（加密私钥）
  - 以 `-----BEGIN PRIVATE KEY-----` 开头（未加密私钥）
- **DER 格式（二进制格式）**：
  - 通常用于 Java 和 Windows 证书存储。

---

## 2. 参数详解

### **1. General options（通用选项）**
| 选项 | 说明 |
|------|------|
| `-help` | 显示帮助信息 |
| `-engine val` | 指定 OpenSSL 引擎（例如硬件设备） |
| `-v1 val` | 使用 **PKCS#5 v1.5** 加密私钥，并指定加密算法（如 `-v1 des3`） |
| `-v2 val` | 使用 **PKCS#5 v2.0** 加密私钥，并指定加密算法（如 `-v2 aes-256-cbc`） |
| `-v2prf val` | 指定 **PKCS#5 v2.0** 的伪随机函数（PRF），如 `-v2prf hmacWithSHA256` |

---

### **2. Input options（输入选项）**
| 选项                 | 说明 |
|--------------------|------|
| `-in infile`       | 指定输入私钥文件 |
| `-inform PEM\|DER` | 指定输入格式，**PEM** 为文本格式，**DER** 为二进制格式 |
| `-passin val`      | 指定输入私钥的密码 |
| `-nocrypt`         | 处理未加密的私钥（明文存储） |

---

### **3. Output options（输出选项）**
| 选项                  | 说明 |
|---------------------|------|
| `-out outfile`      | 指定输出文件 |
| `-outform PEM\|DER` | 指定输出格式，支持 **PEM** 和 **DER** |
| `-topk8`            | 以 PKCS#8 格式输出私钥（默认行为） |
| `-passout val`      | 设置输出文件的密码 |
| `-traditional`      | 以传统 **PKCS#1** 格式输出私钥 |
| `-iter +int`        | 指定 PBKDF2 迭代次数，提高密钥加密强度（默认 2048） |
| `-noiter`           | 使用最小迭代次数（1），降低安全性但加快处理速度 |

---

### **4. Scrypt options（Scrypt 相关选项，用于密码保护）**
| 选项 | 说明 |
|------|------|
| `-scrypt` | 使用 **scrypt** 进行私钥加密（更安全） |
| `-scrypt_N val` | **scrypt N 参数**，默认 `16384`，控制计算强度 |
| `-scrypt_r val` | **scrypt r 参数**，默认 `8`，控制内存使用 |
| `-scrypt_p val` | **scrypt p 参数**，默认 `1`，控制并行计算度 |

---

### **5. Random state options（随机数相关选项）**
| 选项 | 说明 |
|------|------|
| `-rand val` | 从指定文件加载随机数据，以增强随机性 |
| `-writerand outfile` | 将随机数据写入指定文件（用于后续使用） |

---

### **6. Provider options（加密算法提供者选项）**
| 选项 | 说明 |
|------|------|
| `-provider-path val` | 设置 OpenSSL **Provider** 路径（必须在 `-provider` 之前指定） |
| `-provider val` | 指定要加载的 Provider，可多次指定 |
| `-propquery val` | 用于算法查询的属性查询字符串 |


## 3. 用法示例

### **3.1 将 PKCS#1 私钥转换为 PKCS#8（未加密）**
```bash

openssl pkcs8 -topk8 -nocrypt -in rsa_private.pem -out pkcs8_private.pem
```
- `-topk8`：转换为 PKCS#8 格式。
- `-nocrypt`：不加密私钥，输出明文 PKCS#8 格式。

---

### **3.2 使用指定加密算法**
```bash

openssl pkcs8 -topk8 -in rsa_private.pem -out encrypted_pkcs8.pem -v2 aes256 -passout pass:123456
```

[可选算法](https://docs.openssl.org/3.5/man1/openssl-pkcs8/#pkcs5-v15-and-pkcs12-algorithms)

### **3.3 解密 PKCS#8 私钥**
```bash

openssl pkcs8 -in encrypted_pkcs8.pem -out decrypted_private.pem -passin pass:123456
```
- `-passin pass:123456`：输入私钥的解密密码。

---

### **3.4 以 DER 格式导出 PKCS#8 私钥**
```bash

openssl pkcs8 -topk8 -in rsa_private.pem -out pkcs8_private.der -outform DER -nocrypt
```
- `-outform DER`：以二进制格式导出。

---

### **3.5 将 PKCS#8 格式的私钥转换回 PKCS#1**
```bash

openssl rsa -in pkcs8_private.pem -out rsa_private.pem
```
- `openssl rsa` 可以从 PKCS#8 提取标准 RSA 私钥（PKCS#1）。

