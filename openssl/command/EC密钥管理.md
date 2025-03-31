# EC 密钥管理

## 1. `openssl ecparam` 命令

openssl ecparam 命令用于 管理椭圆曲线参数，主要功能包括：

- 生成椭圆曲线参数（EC 参数）
- 查看椭圆曲线参数信息
- 生成 EC 私钥
- 生成 EC 证书请求（CSR）

### 1.1 参数详解

| **类别**        | **选项**             | **说明** |
|---------------|--------------------|------|
| **通用选项** | `-help`            | 显示帮助信息 |
|  | `-list_curves`     | 列出 OpenSSL 支持的所有椭圆曲线 |
|  | `-engine val`      | 指定加密引擎，可能是硬件设备 |
| **输入选项** | `-in infile`       | 指定输入文件（默认 stdin） |
|  | `-inform PEM\|DER` | 指定输入格式（PEM 或 DER，默认为 PEM） |
| **输出选项** | `-out outfile`     | 指定输出文件（默认 stdout） |
|  | `-outform PEM\|DER` | 指定输出格式（PEM 或 DER，默认为 PEM） |
|  | `-text`            | 以文本格式打印 EC 参数 |
|  | `-noout`           | 不输出 EC 参数（通常与 `-text` 组合使用） |
|  | `-param_enc val`   | 指定 EC 参数的编码方式：`explicit`（显式存储）或 `named_curve`（存储曲线名称，默认） |
| **参数管理** | `-check`           | 验证 EC 参数是否有效 |
|  | `-check_named`     | 检查命名的 EC 曲线参数是否被修改 |
|  | `-no_seed`         | 当选择 `explicit` 参数时，不使用种子 |
|  | `-name val`        | 使用指定的椭圆曲线（短名称） |
|  | `-conv_form val`   | 指定点转换格式（`compressed`、`uncompressed` 或 `hybrid`） |
| **随机数选项** | `-rand val`        | 载入指定的随机数文件 |
|  | `-writerand outfile` | 将随机数据写入指定文件 |
| **提供者选项** | `-provider-path val` | 指定加密提供者的路径 |
|  | `-provider val`    | 加载指定的加密提供者（可多次指定） |
|  | `-propquery val`   | 在获取算法时使用属性查询 |

---

### 1.2 椭圆曲线命名规则

常见的椭圆曲线命名一般包括 **三部分**：
1. **类型（标准组织/数学性质）**
2. **密钥长度（比特位）**
3. **可选的后缀（特定特性）**

---

#### NIST 标准曲线（P 系列）
- **格式**：`secpXXXr1` 或 `primeXXXv1`
- **来源**：由美国国家标准与技术研究院（NIST）定义，符合 **FIPS 186-4** 标准
- **特点**：
  - **`secp`**：表示 "SEC Prime"（用于密码学的素数曲线）
  - **`prime`**：表示曲线的基域是素数域（𝔽_p）
  - **`XXX`**：曲线的位数（密钥大小），如 `256`、`384`、`521`
  - **`r1`/`v1`**：表示该版本的曲线（通常 `r1` 表示第一版）


| **曲线名称**  | **位数** | **用途** |
|------------|------|------|
| `secp256r1` | 256  | TLS、数字签名、密钥交换 |
| `prime256v1` | 256  | **等同于 `secp256r1`，只是命名不同** |
| `secp384r1` | 384  | **高安全性 TLS** |
| `secp521r1` | 521  | **超高安全性（大密钥）** |

**说明**：
- `prime256v1` 是 `secp256r1` 的别名，两者**完全相同**。
- `secp256r1` 是 **目前最常用的曲线**，广泛用于 HTTPS 和电子签名。

---

#### SECG 椭圆曲线（secp 系列）
- **格式**：`secpXXXk1`
- **来源**：SEC（Standards for Efficient Cryptography Group）
- **特点**：
  - **`secp`**：表示 "Standards for Efficient Cryptography Prime"
  - **`k1`**：表示使用 **Koblitz 曲线**，比 `r1` 计算速度更快
  - **`XXX`**：曲线的位数

| **曲线名称**  | **位数** | **用途** |
|------------|------|------|
| `secp256k1` | 256  | **比特币、以太坊、区块链** |

**说明**：
- `secp256k1` 采用 **Koblitz 曲线**，计算更快，适用于**区块链签名和交易**。
- **比特币、以太坊** 都使用 `secp256k1`，但它**不适合一般 TLS/HTTPS**。

---

#### Bernstein 椭圆曲线（X 和 Ed 系列）
- **格式**：`X25519`、`Ed25519`
- **来源**：由密码学专家 **Daniel J. Bernstein** 设计
- **特点**：
  - `X25519` 用于 **密钥交换**
  - `Ed25519` 用于 **数字签名**
  - 计算速度极快，抗侧信道攻击，适合 IoT、移动设备、端到端加密


| **曲线名称**  | **位数** | **用途** |
|------------|------|------|
| `X25519` | 256 | **密钥交换**（TLS 1.3 推荐） |
| `Ed25519` | 256 | **数字签名**（SSH、GPG、加密协议） |
| `X448` | 448 | **超高安全密钥交换** |
| `Ed448` | 448 | **超高安全数字签名** |

**说明**：
- `X25519` 在 **TLS 1.3、Signal、WhatsApp** 等广泛使用。
- `Ed25519` 速度快、签名短，被 SSH、GPG 采用。

## 2. `openssl ec` 命令

openssl ec 命令用于 管理椭圆曲线（EC）密钥，包括 查看、转换、导出公钥 以及 检查密钥一致性。

### 参数详解

| **类别**         | **参数**               | **说明** |
|-----------------|----------------------|---------|
| **输入选项**    | `-in val`            | 指定输入密钥文件 |
|                 | `-inform format`     | 指定输入格式，可选 `DER`、`PEM`、`P12`、`ENGINE` |
|                 | `-pubin`             | 指示输入文件为公钥 |
|                 | `-passin val`        | 指定私钥的密码来源 |
|                 | `-check`             | 检查密钥的一致性 |
|                 | `-*`                 | 使用支持的任何加密算法 |
|                 | `-param_enc val`     | 指定椭圆曲线参数的编码方式 |
|                 | `-conv_form val`     | 指定点的转换格式（压缩、未压缩等） |
| **输出选项**    | `-out outfile`       | 指定输出文件 |
|                 | `-outform PEM\|DER`  | 指定输出格式：`DER` 或 `PEM` |
|                 | `-noout`             | 不输出密钥，仅执行操作 |
|                 | `-text`              | 以文本形式打印密钥信息 |
|                 | `-param_out`         | 输出椭圆曲线参数 |
|                 | `-pubout`            | 仅输出公钥，不输出私钥 |
|                 | `-no_public`         | 从私钥文件中排除公钥部分 |
|                 | `-passout val`       | 指定输出文件的密码来源 |
| **Provider 选项** | `-provider-path val` | 指定 OpenSSL 提供者（provider）路径 |
|                 | `-provider val`      | 指定要加载的提供者（可多次指定） |
|                 | `-propquery val`     | 用于获取算法时的属性查询 |



## 3.使用示例

### 3.1 查看支持的椭圆曲线

```bash

openssl ecparam -list_curves
```

### 3.2 生成密钥

```bash

# secp384r1
openssl ecparam -name prime256v1 -genkey -out prime256v1-raw.pem

# secp384r1
openssl ecparam -name secp384r1 -genkey -out secp384r1-raw.pem

# secp521r1
openssl ecparam -name secp521r1 -genkey -out secp521r1-raw.pem


# ed25519 用于数字签名
openssl genpkey -algorithm ed25519 -out ed25519-raw.pem

# x25519 用于密钥交换
openssl genpkey -algorithm x25519 -out x25519-raw.pem

```
注意：
- `genpkey` 生成私钥默认PKCS#8编码


### 3.3 查看私钥/公钥

```bash

# 查看私钥
openssl ec -in ed25519-raw.pem -text -noout

# 查看公钥
openssl ec -pubin -in ed25519.pub -noout -text

```

### 3.4 加密/解密 密钥

```bash

# 加密
openssl pkcs8 -topk8 -v2 aes256 -in ed25519-raw.pem -out ed25519.pem

# 解密
openssl pkcs8 -topk8 -nocrypt -in ed25519.pem -out ed25519-raw.pem

```


### 3.6 提取公钥

```bash

openssl ec -in ed25519-raw.pem -pubout -out ed25519.pub
```

### 3.7 ECDSA签名(secp384r1)

```bash

# 签名
openssl dgst -sha256 -verify secp384r1-raw.pem -out data.sig data

# 验证
openssl dgst -sha256 -verify secp384r1.pub -signature data.sig data

```


### 3.8 EdDSA签名 (ed25519)

Ed25519 和 Ed448 内部自带哈希计算，它们在签名时会自动处理数据的哈希，而不是依赖 `openssl dgst` 手动计算

```bash

# 签名
openssl pkeyutl -sign -inkey ed25519-raw.pem -rawin -in data -out data.sig

# 验证
openssl pkeyutl -verify -pubin -inkey ed25519.pub -sigfile data.sig -rawin -in data 

```

### 3.9 ECDH密钥交换(x25519)

```bash

# server 密钥对

## 私钥
openssl genpkey -algorithm x25519 -out x25519-raw-server.pem
## 公钥
openssl ec -in x25519-raw-server.pem -pubout -out x25519-raw-server.pub

# client 密钥对

## 私钥
openssl genpkey -algorithm x25519 -out x25519-raw-client.pem
## 公钥
openssl ec -in x25519-raw-client.pem -pubout -out x25519-raw-client.pub

# 计算共享密钥

## server 计算
openssl pkeyutl -derive -inkey x25519-raw-server.pem -peerkey x25519-raw-client.pub -out server.bin
## client 计算
openssl pkeyutl -derive -inkey x25519-raw-client.pem -peerkey x25519-raw-server.pub -out client.bin

# 检查

## 检查server.bin
base64 server.bin
mMsyqXNK+XuaMB5dpx7/QQPXSthYEYcBK1PyXVBOhRQ=

## 检查client.bin
base64 client.bin
mMsyqXNK+XuaMB5dpx7/QQPXSthYEYcBK1PyXVBOhRQ=
```
