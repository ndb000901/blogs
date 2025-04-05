
# PBKDF

PBKDF（基于密码的密钥派生函数，Password-Based Key Derivation Function）是一种用于**从用户密码派生加密密钥**的算法，广泛用于**加密、哈希存储、认证**等领域，以增强密码的安全性。

---

## **1. PBKDF 的作用**

1. **密码哈希存储**：用于存储用户密码，防止彩虹表攻击。
2. **密钥派生**：将用户密码转换为加密密钥（如 AES-256 的 256-bit 密钥）。
3. **增强密码强度**：通过多次哈希和盐值（salt）抵抗暴力破解。

---

## **2. PBKDF 主要算法**

PBKDF 有多个实现方式，常见的包括：

| 算法 | 说明 |
|------|------|
| **PBKDF1** | 旧版本，不推荐使用，最大密钥长度 160-bit。 |
| **PBKDF2** | 推荐使用，支持任意长度密钥，广泛用于 TLS、OpenSSL、Wi-Fi WPA2。 |
| **bcrypt** | 适用于密码存储，计算强度可调，抗 GPU 计算。 |
| **scrypt** | 计算、存储强度可调，抗 FPGA 和 ASIC 硬件破解，适用于区块链（如 Bitcoin）。 |
| **Argon2** | 目前最先进的 PBKDF，2015 年获奖，适用于现代密码存储。 |

---

## **3. PBKDF2 详解**
### **PBKDF2 计算过程**
PBKDF2 采用**HMAC（哈希消息认证码）** 函数进行多次迭代计算，流程如下：
1. **输入密码（Password）**。
2. **生成随机盐值（Salt）**，防止预计算攻击。
3. **迭代哈希（Iterations）**，例如 100,000 次 HMAC-SHA256。
4. **生成加密密钥（Derived Key）**，用于 AES、RSA 等加密算法。

### **PBKDF2 参数**
| 参数 | 说明 |
|------|------|
| **Password** | 用户输入的密码 |
| **Salt** | 随机生成的盐值，通常 128-bit |
| **Iterations** | 迭代次数，推荐 **100,000+**（OpenSSL 默认 2048） |
| **Key Length** | 生成的密钥长度，如 256-bit（AES-256） |
| **PRF（伪随机函数）** | 通常为 HMAC-SHA256、HMAC-SHA512 |

### **PBKDF2 计算公式**
```text
DK = PBKDF2(PRF, Password, Salt, Iterations, KeyLength)
```
其中：
- **PRF**（伪随机函数）：常用 HMAC-SHA256。
- **Iterations**（迭代次数）：越大越安全，但计算更慢。
- **KeyLength**（输出密钥长度）：如 AES-256 需要 256-bit。

---

## **4. scrypt 详解**
### **scrypt 计算过程**
scrypt 是 PBKDF2 的增强版，专为**防 GPU 计算攻击**设计，流程如下：
1. **密码和盐值输入**。
2. **大量内存填充（Memory-Hard）**，防止 GPU 并行计算。
3. **迭代哈希计算（CPU-intensive）**，提升破解难度。
4. **输出密钥（Derived Key）**。

### **scrypt 参数**
| 参数 | 说明 |
|------|------|
| **Password** | 用户输入的密码 |
| **Salt** | 随机盐值，通常 128-bit |
| **N** | 计算成本因子，推荐 16384（2¹⁴） |
| **r** | 内存占用因子，推荐 8 |
| **p** | 并行度，推荐 1 |
| **Key Length** | 生成的密钥长度 |

### **scrypt 计算公式**
```text
DK = scrypt(Password, Salt, N, r, p, KeyLength)
```


**scrypt 优势**
- **抗 GPU、FPGA、ASIC** 破解（比 PBKDF2 更安全）。
- **适用于密码存储和区块链钱包（Bitcoin）**。
- **计算强度更大，占用更多内存**。

---

## **5. PBKDF2 vs. scrypt vs. bcrypt vs. Argon2**
| 算法 | 安全性 | 速度 | 适用场景 |
|------|------|------|------|
| **PBKDF2** | 中等 | 快 | TLS、WPA2、存储 |
| **scrypt** | 高 | 慢 | 区块链、存储 |
| **bcrypt** | 高 | 中等 | Web 应用密码存储 |
| **Argon2** | 最高 | 可调 | 密码存储、区块链 |

---

## **6. OpenSSL PBKDF2 命令**
### **生成 PBKDF2 密钥**
```bash

openssl enc -aes-256-cbc -pbkdf2 -pass pass:123456 -salt -in plaintext.txt -out encrypted.txt
```
- `-pbkdf2`：启用 PBKDF2。
- `-aes-256-cbc`：使用 AES-256 加密。
- `-pass pass:123456`：输入密码。

### **验证 PBKDF2 密钥**
```bash

openssl enc -d -aes-256-cbc -pbkdf2 -pass pass:123456 -in encrypted.txt -out decrypted.txt
```
- `-d`：解密。
- `-pbkdf2`：使用 PBKDF2 密钥派生。

---

