# RSA密钥管理

# 一、生成密钥

## 1.使用 `openssl genrsa` 命令

`openssl genrsa` 用于 **生成 RSA 私钥**，可通过不同选项控制密钥参数、输出格式及加密方式。

### 1.1 参数说明

#### 输入选项（控制公钥指数）

| 选项 | 说明 | 示例 |
|------|------|------|
| `-3` _(已废弃)_ | 使用 `3` 作为公钥指数（`e=3`，已被淘汰） | `openssl genrsa -3 -out private.pem 2048` |
| `-F4` | 使用费马数 `F4` (`e=65537`, 推荐) | `openssl genrsa -F4 -out private.pem 2048` |
| `-f4` | `-F4` 的别名，效果相同 | `openssl genrsa -f4 -out private.pem 2048` |

> **推荐**：使用 `-F4` (`e=65537`)，更安全、兼容性更好。

---

#### 输出选项

| 选项 | 说明 | 示例 |
|------|------|------|
| `-out <file>` | 指定私钥输出文件 | `-out private.pem` |
| `-passout <val>` | 设置输出文件的加密密码 | `-passout pass:MySecret` |
| `-primes <int>` | 指定质数个数（默认 2，支持多质数 RSA） | `-primes 3` |
| `-verbose` | 输出详细信息 | `-verbose` |
| `-traditional` | 生成 **传统格式** 的私钥（不带 `PKCS#8` 头） | `-traditional` |
| `-*` | 使用指定加密算法加密私钥 | `-aes256`（AES-256 加密私钥） |

> **`-primes` 说明**：  
> - **默认 `-primes 2`**：标准 RSA（两个质数）  
> - **支持 `-primes 3` 或更高**：生成 **多质数 RSA**，提高加密性能（但兼容性较低）  

---

#### 随机数选项
| 选项 | 说明 | 示例 |
|------|------|------|
| `-rand <file>` | 指定随机数种子文件 | `-rand /dev/urandom` |
| `-writerand <file>` | 将生成的随机数据写入文件 | `-writerand random.dat` |

---

#### Provider 相关选项
| 选项 | 说明 | 示例 |
|------|------|------|
| `-provider-path <path>` | 指定 provider 加载路径 | `-provider-path /usr/lib/openssl-providers` |
| `-provider <val>` | 加载指定 provider（可多次指定） | `-provider legacy -provider default` |
| `-propquery <val>` | 设定 provider 查询参数 | `-propquery "provider=default"` |

---

#### 关键参数
| 参数 | 说明 | 示例 |
|------|------|------|
| `numbits` | 指定密钥长度（位） | `openssl genrsa -out private.pem 2048` |

> **推荐**：
> - **最小 2048 位**（`2048`）：标准安全级别  
> - **更安全 4096 位**（`4096`）：适用于高安全需求

---

### 1.2 使用示例

#### 生成 2048 位 RSA 私钥**
```bash

openssl genrsa -out private.pem 2048
```

#### 生成 4096 位加密 RSA 私钥
```bash

openssl genrsa -aes256 -out private.pem 4096
```

#### 生成带密码的私钥
```bash

openssl genrsa -aes256 -passout pass:MySecret -out private.pem 2048
```

#### 生成三质数 RSA 私钥
```bash

openssl genrsa -primes 3 -out private.pem 2048
```

#### 使用 `/dev/urandom` 作为随机数源
```bash

openssl genrsa -rand /dev/urandom -out private.pem 2048
```

## 2.使用 `openssl genpkey` 命令

[密钥生成](./密钥生成.md)


## 3.使用 `openssl rsa` 命令


### 可选参数

#### General Options

| 选项          | 描述                                                            |
|---------------|-----------------------------------------------------------------|
| `-help`       | 显示帮助信息（即该命令的简要说明）。                               |
| `-check`      | 验证密钥的一致性，检查 RSA 密钥对是否有效。                        |
| `-*`          | 支持的任何密码算法，用于加密或解密密钥。                           |
| `-engine val` | 使用特定的加密引擎，可能是硬件设备等。                             |

#### Input Options

| 选项               | 描述                                                            |
|--------------------|-----------------------------------------------------------------|
| `-in val`          | 输入文件路径，即需要操作的文件。                                  |
| `-inform format`   | 输入格式，可以选择 `DER`、`PEM`、`P12` 或 `ENGINE`（默认 `PEM` 格式）。 |
| `-pubin`           | 输入文件是公钥而非私钥。                                          |
| `-RSAPublicKey_in` | 输入文件是 RSA 公钥，适用于导入 RSA 格式的公钥。                  |
| `-passin val`      | 输入文件的密码来源。可以是文件或命令行输入的密码。               |

#### Output Options

| 选项                  | 描述                                                              |
|-----------------------|-------------------------------------------------------------------|
| `-out outfile`         | 输出文件路径，即生成的密钥文件保存位置。                           |
| `-outform format`      | 输出格式，可以选择 `DER`、`PEM`、`PVK` 格式。                     |
| `-pubout`              | 输出公钥而非私钥。                                                  |
| `-RSAPublicKey_out`    | 输出为 RSA 公钥格式。                                               |
| `-passout val`         | 输出文件的密码来源。可以是文件或命令行输入的密码。                |
| `-noout`               | 不输出密钥本身，仅输出其他信息（如模数、文本等）。                  |
| `-text`                | 打印密钥的详细信息（以文本格式显示）。                              |
| `-modulus`             | 打印 RSA 密钥的模数。                                               |
| `-traditional`         | 使用传统格式输出私钥。                                             |

#### PVK Options

| 选项                | 描述                                                          |
|---------------------|---------------------------------------------------------------|
| `-pvk-strong`       | 启用强 PVK 编码级别（默认设置）。                              |
| `-pvk-weak`         | 启用弱 PVK 编码级别。                                          |
| `-pvk-none`         | 不强制使用 PVK 编码。                                          |

#### Provider Options

| 选项                  | 描述                                                                |
|-----------------------|---------------------------------------------------------------------|
| `-provider-path val`   | 提供加载路径（如果需要加载提供者，必须在 `provider` 选项之前指定）。    |
| `-provider val`        | 加载一个或多个加密提供者。                                           |
| `-propquery val`       | 从提供者查询算法的属性。                                             |


# 二、转换操作

## 1.格式转换

### 1.1 从 PEM 格式导出私钥到 DER 格式
```bash

openssl rsa -in private.pem -outform DER -out private.der
```
- `-in private.pem`: 输入 PEM 格式的私钥文件。
- `-outform DER`: 输出格式为 DER 格式。
- `-out private.der`: 输出文件为 `private.der`。

### 1.2 从 DER 格式导入并导出为 PEM 格式的私钥
```bash

openssl rsa -in private.der -inform DER -out private.pem -outform PEM
```
- `-in private.der`: 输入 DER 格式的私钥文件。
- `-inform DER`: 指定输入格式为 DER。
- `-out private.pem`: 输出为 PEM 格式的私钥文件。
- `-outform PEM`: 指定输出格式为 PEM。

## 2. 规范转换

### 2.1 `PKCS#1 -> PKCS#8`

```bash

# 加密输出
openssl pkcs8 -topk8 -inform PEM -outform PEM -in p1-raw.pem -out p8.pem -v2 aes256

# 不加密输出
openssl pkcs8 -topk8 -nocrypt -inform PEM -outform PEM -in p1-raw.pem -out p8-raw.pem

```
- `-nocrypt`: 不加密私钥，输出明文 PKCS#8 格式。
- 默认加密算法: aes256

### 2.2 `PKCS#8 -> PKCS#1`

```bash

# 加密输出
openssl rsa -aes256 -in p8.pem -out p1.pem

# 不加密输出
openssl rsa -in p8.pem -out p1-raw.pem

```

## 3. 加密/解密私钥

### 3.1 加密/解密 PCKS#1 私钥

```bash

# 加密
openssl rsa -aes256 -inform PEM -in p1-raw.pem -out p1.pem

# 解密
openssl rsa -in p1.pem -out p1-raw.pem
```

### 3.2 加密/解密 PKCS#8 私钥

```bash

# 加密
openssl pkcs8 -topk8 -v2 aes256 -in p8-raw.pem -out p8.pem

# 解密
openssl pkcs8 -topk8 -nocrypt -in p8.pem -out p8-raw.pem

```

## 4.提取公钥

```bash

# PCKS#1

openssl rsa -pubout -in p1-raw.pem -out p1.pub

# PKCS#8
openssl rsa -pubout -in p8-raw.pem -out p8.pub
```

## 5.查看信息

```bash

# 查看公钥

openssl rsa -pubin  -in p1.pub -noout -text

# 查看私钥

openssl rsa -in p1-raw.pem -noout -text
```

## 6.签名测试

```bash

# 生成签名
openssl dgst -sha256 -sign p1-raw.pem -out data.sig data

# 验证签名
openssl dgst -sha256 -verify p1.pub -signature data.sig data

```

## 7.加密测试

```bash

# 公钥加密
openssl pkeyutl -encrypt -pubin -inkey p1.pub -in data -out data.bin

# 私钥解密

openssl pkeyutl -decrypt -inkey p1-raw.pem -in data.bin -out data.txt

```









