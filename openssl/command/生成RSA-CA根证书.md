# 生成RSA CA根证书


## 1.初始化

工作目录: `/home/hello/code/openssl/demo/rsa-ca-demo/root-dir`

```bash

# 创建工作目录
export CA_ROOT_DIR=/home/hello/code/openssl/demo/rsa-ca-demo/root-dir
mkdir -p $CA_ROOT_DIR

# 切换到工作目录
cd $CA_ROOT_DIR

# 存放证书吊销列表 (CRL)
# 签发的证书(issued_certs)
mkdir -p crl issued_certs
# 证书数据库 (`index.txt` 记录已签发证书)
touch index.txt

# 生成serial文件，用于储存CA证书序列号(下一个要使用的序列号，16进制)
openssl rand -hex 16 > $CA_ROOT_DIR/serial

# 用于吊销证书，每次生成新的 CRL 时，它会递增，确保 CRL 唯一且可追溯。
echo "01" >> $CA_ROOT_DIR/crlnumber
```


## 2.生成CA根证书 RSA密钥

```bash

export CA_ROOT_KEY_PATH=$CA_ROOT_DIR/key.pem

# 加密
openssl genrsa -aes56 -out $CA_ROOT_KEY_PATH 4096
# 不加密
openssl genrsa -out $CA_ROOT_KEY_PATH 4096


```

## 3.生成证书签名请求配置文件


```bash

export CA_ROOT_REQ_CONFIG_PATH=$CA_ROOT_DIR/root-req.conf
cat > $CA_ROOT_REQ_CONFIG_PATH << EOL
# https://docs.openssl.org/3.0/man1/openssl-req/#configuration-file-format
[ req ]

distinguished_name  = req_distinguished_name
string_mask         = utf8only
prompt              = no

# SHA-1 is deprecated, so use SHA-2 instead.
default_md          = sha384

# Extension to add when the -x509 option is used.
x509_extensions     = v3_ca
req_extensions     = v3_ca

[ req_distinguished_name ]
# See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
countryName                     = US
0.organizationName              = Hello Studio
organizationalUnitName          = www.hello.work
commonName                      = Hello CA RSA Root

# https://docs.openssl.org/3.0/man5/x509v3_config/#key-usage
[ v3_ca ]

subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign
EOL
```

### `req` 配置段

| 配置项 | 作用 |
|--------|------|
| `distinguished_name = req_distinguished_name` | 定义证书的主题信息，使用 `[ req_distinguished_name ]` 配置段。 |
| `string_mask = utf8only` | 证书字符串采用 UTF-8 编码，确保国际字符兼容性。 |
| `prompt = no` | 生成证书时 **不交互输入**，直接使用配置文件中的值。 |
| `default_md = sha384` | **默认哈希算法**，使用 `SHA-384`，比 `SHA-256` 更安全。 |
| `x509_extensions = v3_ca` | 仅在 `openssl req -x509` 生成 **自签名 CA 证书** 时，应用 `[ v3_ca ]` 里的扩展信息。 |
| `req_extensions = v3_ca` | 在生成 **证书签名请求（CSR）** 时，同样应用 `[ v3_ca ]` 里的扩展信息。 |

---

### `req_distinguished_name` 配置段

该部分定义证书的 **主题信息（Subject DN）**，即证书持有者的信息：

| 字段 | 作用 |
|------|------|
| `countryName = US` | 证书所属国家（美国）。 |
| `0.organizationName = Hello Studio` | 证书所属组织（Hello Studio 公司）。 |
| `organizationalUnitName = www.hello.work` | 组织单元（部门），这里填写的是网站域名。 |
| `commonName = Hello CA RSA Root` | 证书的 **通用名称（CN）**，这里是 `Hello CA RSA Root`，表示这是一个 CA 证书。 |

**📌 备注**：
- 这个 `commonName` 不能用于 HTTPS 服务器证书，因为 CA 证书不会被浏览器直接信任，必须导入到 **系统信任存储**。

---

### `v3_ca` 配置段

这个部分是 **X.509 v3 扩展**，用于定义 CA 证书的用途。

| 配置项 | 作用 |
|--------|------|
| `subjectKeyIdentifier = hash` | 计算并填充 `Subject Key Identifier`（SKID），用于唯一标识该 CA 证书的公钥。 |
| `authorityKeyIdentifier = keyid:always,issuer:always` | `AKID`（Authority Key Identifier），标记 **CA 证书** 的 `SKID` 和 `Issuer` 信息，帮助验证证书链。 |
| `basicConstraints = critical, CA:true` | **必须是 CA 证书**，并将其标记为 `critical`（必须被验证，否则拒绝证书）。 |
| `keyUsage = critical, digitalSignature, cRLSign, keyCertSign` | **密钥用途**（Key Usage）：<br>✅ `digitalSignature`：允许数字签名（用于 OCSP 服务器或身份验证）<br>✅ `cRLSign`：允许签发 **证书吊销列表（CRL）**<br>✅ `keyCertSign`：允许使用此 CA 证书 **签发下级证书**。 |



## 4.自签名CA根证书

```bash

export CA_ROOT_CERT_PATH=$CA_ROOT_DIR/ca.pem

# 生成一个有效期为 100 年的自签名 CA 根证书
openssl req -config $CA_ROOT_REQ_CONFIG_PATH \
    -new \
    -x509 \
    -extensions v3_ca \
    -days 36500 \
    -key $CA_ROOT_KEY_PATH \
    -out $CA_ROOT_CERT_PATH

```

### 查看证书信息

```bash
openssl x509 -in $CA_ROOT_CERT_PATH -noout -text
```

### 自验证

由于是根证书，只能自己验证自己

```bash
openssl verify -CAfile $CA_ROOT_CERT_PATH $CA_ROOT_CERT_PATH
# 输出: ca.pem: OK
```

## 5.配置CA

用于签发别的证书

```bash

export CA_ROOT_CONF_PATH=$CA_ROOT_DIR/ca.cnf

cat > $CA_ROOT_CONF_PATH << EOL

# https://docs.openssl.org/3.0/man1/openssl-ca/#configuration-file-options
[ ca ]
default_ca = CA_default

[ CA_default ]
# Directory and file locations.
dir               = $CA_ROOT_DIR
certs             = \$dir/certs
crl_dir           = \$dir/crl
new_certs_dir     = \$dir/issued_certs
database          = \$dir/index.txt
serial            = \$dir/serial
RANDFILE          = \$dir/.rand

# The root key and root certificate.
private_key       = \$dir/key.pem
certificate       = \$dir/ca.pem

# For certificate revocation lists.
crlnumber         = \$dir/crlnumber
crl               = \$dir/crl/ca.crl.pem
crl_extensions    = crl_ext
default_crl_days  = 30
copy_extensions   = copy

# SHA-1 is deprecated, so use SHA-2 instead.
default_md        = sha256

name_opt          = ca_default
cert_opt          = ca_default
default_days      = 375
preserve          = no
policy            = policy_strict

# https://docs.openssl.org/3.0/man1/openssl-ca/#policy-format
[ policy_strict ]
# The root CA should only sign intermediate certificates that match.
countryName             = match
stateOrProvinceName     = optional
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ policy_loose ]
# Allow the intermediate CA to sign a more diverse range of certificates.
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

# https://docs.openssl.org/3.4/man1/openssl-ca/#configuration-file-options
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
prompt              = no

# SHA-1 is deprecated, so use SHA-2 instead.
default_md          = sha384

# Extension to add when the -x509 option is used.
x509_extensions     = v3_ca

[ req_distinguished_name ]
# See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
countryName                     = US
0.organizationName              = Hello Studio
organizationalUnitName          = www.hello.work
commonName                      = Hello CA RSA Root

# https://docs.openssl.org/3.4/man5/x509v3_config/#key-usage
[ v3_ca ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ v3_intermediate_ca ]
keyUsage = critical, digitalSignature, cRLSign, keyCertSign
extendedKeyUsage = critical, clientAuth, serverAuth
basicConstraints = critical, CA:true, pathlen:0
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
# authorityInfoAccess = caIssuers;URI:http://demo.org/ca.html
# crlDistributionPoints = URI:http://demo.org/ca.crl
# certificatePolicies = 2.23.140.1.2.1,@policy_issuer_info

# [ policy_issuer_info ]
# policyIdentifier = 1.3.6.1.4.1.44947.1.2.3.4.5.6.7.8

[ crl_ext ]
authorityKeyIdentifier=keyid:always

# https://docs.openssl.org/3.4/man5/x509v3_config/#description
[ ocsp ]
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
keyUsage = critical, digitalSignature
extendedKeyUsage = critical, OCSPSigning
EOL
```

### **1. [ca] & [CA_default]：CA 配置**
这些部分配置了 CA 的默认行为和文件存储路径。  

#### **[ca] 部分**
```ini
[ ca ]
default_ca = CA_default
```
- 指定默认 CA 配置为 `CA_default`，即 OpenSSL 运行 CA 相关命令时，默认使用 `[CA_default]` 里的配置。

#### **[CA_default] 部分**
```ini
[ CA_default ]
# Directory and file locations.
dir               = $CA_ROOT_DIR
certs             = \$dir/certs
crl_dir           = \$dir/crl
new_certs_dir     = \$dir/issued_certs
database          = \$dir/index.txt
serial            = \$dir/serial
RANDFILE          = \$dir/.rand
```
- `dir`：CA 相关文件的根目录（`$CA_ROOT_DIR`）。
- `certs`：存放签发的证书。
- `crl_dir`：存放证书吊销列表（CRL）。
- `new_certs_dir`：新签发的证书存储位置。
- `database`：证书数据库文件（`index.txt`），记录所有签发的证书。
- `serial`：记录证书序列号的文件。
- `RANDFILE`：随机数种子文件。

```ini
# The root key and root certificate.
private_key       = \$dir/key.pem
certificate       = \$dir/ca.pem
```
- `private_key`：根 CA 私钥路径。
- `certificate`：根 CA 证书路径。

```ini
# For certificate revocation lists.
crlnumber         = \$dir/crlnumber
crl               = \$dir/crl/ca.crl.pem
crl_extensions    = crl_ext
default_crl_days  = 30
copy_extensions   = copy
```
- `crlnumber`：记录 CRL（证书吊销列表）编号。
- `crl`：CRL 文件路径。
- `crl_extensions`：CRL 扩展配置（参考 `[crl_ext]` 部分）。
- `default_crl_days`：CRL 默认有效期 30 天。
- `copy_extensions`：允许证书继承请求中的扩展字段。

```ini
# SHA-1 is deprecated, so use SHA-2 instead.
default_md        = sha256
```
- `default_md`：使用 `SHA-256` 作为签名算法，避免已过时的 `SHA-1`。

```ini
name_opt          = ca_default
cert_opt          = ca_default
default_days      = 375
preserve          = no
policy            = policy_strict
```
- `default_days`：签发证书的默认有效期（375 天）。
- `preserve`：是否保持证书请求中的 `DN`（Distinguished Name），默认 `no`。
- `policy`：证书签发策略（参考 `[policy_strict]`）。

---

### **2. [policy_strict] & [policy_loose]：证书签发策略**
证书签发策略用于 CA 验证申请证书的信息是否符合规则。  

#### **严格策略（`policy_strict`）**
```ini
[ policy_strict ]
countryName             = match
stateOrProvinceName     = optional
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional
```
- `countryName = match`：申请证书的 `countryName` 必须与 CA 相同。
- `organizationName = match`：`organizationName` 也必须匹配 CA。
- `commonName = supplied`：`commonName` 必须提供，但不需要匹配 CA。
- 其他字段可选。

#### **宽松策略（`policy_loose`）**
```ini
[ policy_loose ]
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional
```
- 允许签发各种不同国家、组织的证书。
- 只要求提供 `commonName`，其余字段可选。

---

### **3. [req] & [req_distinguished_name]：证书请求（CSR）配置**
控制 `openssl req` 生成 CSR（证书签名请求）的行为。

```ini
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
prompt              = no
default_md          = sha384
x509_extensions     = v3_ca
```
- `default_bits = 4096`：默认生成 4096 位 RSA 密钥。
- `string_mask = utf8only`：强制使用 UTF-8 编码。
- `prompt = no`：不交互询问 `DN`（直接从 `[req_distinguished_name]` 读取）。
- `default_md = sha384`：默认签名算法 `SHA-384`。
- `x509_extensions = v3_ca`：如果 `-x509` 选项生成自签名证书，使用 `[v3_ca]` 扩展。

```ini
[ req_distinguished_name ]
countryName                     = US
0.organizationName              = Hello Studio
organizationalUnitName          = www.hello.work
commonName                      = Hello CA RSA Root
```
- `countryName`：国家代码 `US`。
- `organizationName`：组织名称 `Hello Studio`。
- `organizationalUnitName`：组织单位 `www.hello.work`。
- `commonName`：证书通用名称 `Hello CA RSA Root`。

---

### **4. [v3_ca] & [v3_intermediate_ca]：CA 证书扩展**
定义 CA 证书的 X.509 v3 扩展字段。

#### **根 CA 配置（`v3_ca`）**
```ini
[ v3_ca ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign
```
- `basicConstraints = CA:true`：表明是 CA 证书。
- `keyUsage`：允许 CA 签名证书 (`keyCertSign`)、签署 CRL (`cRLSign`)、数字签名 (`digitalSignature`)。

#### **中间 CA 配置（`v3_intermediate_ca`）**
```ini
[ v3_intermediate_ca ]
keyUsage = critical, digitalSignature, cRLSign, keyCertSign
extendedKeyUsage = critical, clientAuth, serverAuth
basicConstraints = critical, CA:true, pathlen:0
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
```
- `basicConstraints = CA:true, pathlen:0`：是 CA 但不能再签发 CA 证书（只能签发用户证书）。
- `extendedKeyUsage = clientAuth, serverAuth`：可用于 TLS 服务器和客户端认证。

---

### **5. [crl_ext] & [ocsp]：CRL 和 OCSP 配置**
#### **CRL 扩展**
```ini
[ crl_ext ]
authorityKeyIdentifier=keyid:always
```
- `authorityKeyIdentifier`：指示 CRL 由哪个 CA 签发。

#### **OCSP 配置**
```ini
[ ocsp ]
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
keyUsage = critical, digitalSignature
extendedKeyUsage = critical, OCSPSigning
```
- `basicConstraints = CA:FALSE`：OCSP 不是 CA。
- `extendedKeyUsage = OCSPSigning`：该证书可用于 OCSP 签名。

---


