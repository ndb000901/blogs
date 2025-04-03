# 生成RSA CA中间证书

## 1.初始化

工作目录: `/home/hello/code/openssl/demo/rsa-ca-demo/l2-dir`

```bash

# 创建工作目录
export CA_L2_DIR=/home/hello/code/openssl/demo/rsa-ca-demo/l2-dir
mkdir -p $CA_L2_DIR

# 切换到工作目录
cd $CA_L2_DIR

# 存放证书吊销列表 (CRL)
# 签发的证书(issued_certs)
mkdir -p crl issued_certs
# 证书数据库 (`index.txt` 记录已签发证书)
touch index.txt

# 生成serial文件，用于储存CA证书序列号(下一个要使用的序列号，16进制)
openssl rand -hex 16 > $CA_L2_DIR/serial

# 用于吊销证书，每次生成新的 CRL 时，它会递增，确保 CRL 唯一且可追溯。
echo "01" >> $CA_ROOT_DIR/crlnumber
```


## 2.生成CA中间证书 RSA密钥

```bash
export CA_L2_KEY_PATH=$CA_L2_DIR/key.pem
# 加密
openssl genrsa -aes256 -out $CA_L2_KEY_PATH 4096

# 不加密
openssl genrsa -out $CA_L2_KEY_PATH 4096

```

## 3.生成签名请求配置

[参数说明](./生成RSA-CA根证书.md)

```bash

export CA_L2_REQ_CONFIG_PATH=$CA_L2_DIR/ca.csr.cnf

cat > $CA_L2_REQ_CONFIG_PATH << EOL
[ req ]
distinguished_name  = req_distinguished_name
string_mask         = utf8only

# SHA-1 is deprecated, so use SHA-2 instead.
default_md          = sha384
prompt              = no

[ req_distinguished_name ]
# See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
countryName                     = US
0.organizationName              = Hello Studio
organizationalUnitName          = www.hello.work
commonName                      = Hello_L2 CA RSA R1
EOL
```

## 4. 生成签名请求

```bash

export CA_L2_CSR_PATH=$CA_L2_DIR/ca.csr.pem

openssl req -config $CA_L2_REQ_CONFIG_PATH -new -key $CA_L2_KEY_PATH -out $CA_L2_CSR_PATH
```

- `-config`：指定 OpenSSL 配置文件（通常是 .cnf 文件），其中定义了 CSR 的 默认字段（如国家、组织、通用名等）以及扩展字段（如 keyUsage、basicConstraints）。

- `-new`：表示生成新的 CSR（Certificate Signing Request）。

- `-key`：指定用于生成 CSR 的私钥文件路径（一般是 .pem 或 .key 文件）。

- `-out`：指定生成的 CSR 文件的输出路径（通常是 .csr 文件）

### 查看请求信息

```bash
openssl req -in $CA_L2_CSR_PATH -noout -text
```


## 5.签发CA中间证书

使用根CA签发 二级CA证书

```bash

export CA_L2_CERT_PATH=$CA_L2_DIR/ca.pem
openssl ca \
    -config $CA_ROOT_DIR/ca.cnf \
    -extensions v3_intermediate_ca \
    -days 3650 \
    -notext \
    -batch \
    -in $CA_L2_CSR_PATH \
    -out $CA_L2_CERT_PATH
```

### 查看证书信息

```bash

openssl x509 -noout -text -in $CA_L2_CERT_PATH
```

### 验证签名

使用根CA验证

```bash

openssl verify -CAfile $CA_ROOT_CERT_PATH $CA_L2_CERT_PATH
# /home/hello/code/openssl/demo/rsa-ca-demo/l2-dir/ca.pem: OK
```

## 6.生成证书链文件

```bash

export CA_L2_CERT_CHAIN_PATH=$CA_L2_DIR/ca.fullchain.pem

cat > $CA_L2_CERT_CHAIN_PATH << EOL
$(cat $CA_L2_CERT_PATH)

$(cat $CA_ROOT_CERT_PATH)
EOL
```

### **证书链的结构**
一般来说，证书链文件包含多个证书，顺序如下：
1. **服务器证书（Leaf Certificate）**
2. **中级 CA 证书（Intermediate CA Certificate）**
3. **根 CA 证书（Root CA Certificate）**（通常不包含，客户端一般已经内置）

**示例：**
```pem
-----BEGIN CERTIFICATE-----
# 服务器证书（Leaf Certificate）
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A...
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
# 中级 CA 证书（Intermediate CA Certificate）
MIICjTCCAXUCAQE...
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
# 根 CA 证书（Root CA Certificate，通常不包含）
MIIEIDCCAwigAwIB...
-----END CERTIFICATE-----
```
**注意顺序**：
- **最上面是服务器证书**
- **然后是中级 CA 证书**
- **根 CA 证书一般不会包含**，因为大部分操作系统/浏览器已经预装了常见的根证书。


## 7.配置CA

```bash

export CA_L2_CONF_PATH=$CA_L2_DIR/ca.cnf

cat > $CA_L2_CONF_PATH << EOL
# OpenSSL intermediate CA configuration file.
# Copy to /root/ca/intermediate/openssl.cnf.

[ ca ]
# man ca
default_ca = CA_default

[ CA_default ]
# Directory and file locations.
dir               = $CA_L2_DIR
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
crl               = \$dir/crl/intermediate.crl.pem
crl_extensions    = crl_ext
default_crl_days  = 30

# SHA-1 is deprecated, so use SHA-2 instead.
default_md        = sha256

name_opt          = ca_default
cert_opt          = ca_default
default_days      = 375
preserve          = no
policy            = policy_loose
copy_extensions   = copy

[ policy_loose ]
# Allow the intermediate CA to sign a more diverse range of certificates.
# See the POLICY FORMAT section of the ca man page.
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ client_cert ]
# Extensions for client certificates (man x509v3_config).
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage = clientAuth
# authorityInfoAccess = caIssuers;URI:http://demo.org/ca.html
# certificatePolicies = 2.23.140.1.2.1,@policy_issuer_info
# authorityInfoAccess = OCSP;URI:http://ocsp.demo.org/

[ server_cert ]
# Extensions for server certificates (man x509v3_config).
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth, clientAuth
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
# authorityInfoAccess = caIssuers;URI:http://demo.org/ca.html
# certificatePolicies = 2.23.140.1.2.1,@policy_issuer_info
# authorityInfoAccess = OCSP;URI:http://ocsp.demo.org/

# [ policy_issuer_info ]
# policyIdentifier = 1.3.6.1.4.1.44947.1.2.3.4.5.6.7.8
# CPS.1 = "http://cps.demo.org/"
# userNotice.1 = @policy_issuer_notice

# [ policy_issuer_notice ]

# explicitText="This is a demo certificate"
# organization="Demo ORG"

[ crl_ext ]

# CRL extensions.
# Only issuerAltName and authorityKeyIdentifier make any sense in a CRL.

# issuerAltName=issuer:copy
authorityKeyIdentifier=keyid:always

EOL

```


### **`[ client_cert ]` 段落**

这个段落定义了专门用于**客户端证书**的扩展。客户端证书通常用于向服务器证明客户端的身份（例如，在 TLS 双向认证或 mTLS 中）。

* **`basicConstraints = CA:FALSE`**:
    * **作用**: 基本约束扩展。用于指明该证书的主体（Subject）是否是一个证书颁发机构（CA）。
    * **`CA:FALSE`**: 表示这个证书**不是** CA 证书，它不能用于签发其他证书。这是一个**终端实体（End-entity）**证书。对于客户端证书来说，这几乎总是必需的设置，以防止客户端证书被误用作 CA。

* **`subjectKeyIdentifier = hash`**:
    * **作用**: 主体密钥标识符 (SKI)。提供一种从证书中包含的公钥派生出来的标识符。
    * **`hash`**: 表示该标识符是根据公钥信息计算出的哈希值（通常是 SHA-1）。SKI 主要用于在构建和验证证书链时区分可能拥有相同主体名称但不同密钥对的多个证书，是 AKI 引用 issuer 密钥的基础。

* **`authorityKeyIdentifier = keyid:always,issuer:always`**:
    * **作用**: 颁发机构密钥标识符 (AKI)。提供识别**签发此证书的 CA** 所使用的公钥的方法，帮助验证程序找到正确的颁发者证书以构建信任链。
    * **`keyid:always`**: 表示**总是**包含颁发者证书的 `subjectKeyIdentifier` 值。这是 AKI 最常用的形式，允许验证程序通过密钥标识符匹配子证书和父（颁发者）证书。
    * **`issuer:always`**: 表示**总是**包含颁发者证书的**颁发者名称（Issuer Name）**和**序列号（Serial Number）**。这提供了另一种识别颁发者证书的方式。同时包含 `keyid` 和 `issuer` 可以提供更强的唯一性保证，但 `keyid` 在路径构建中更常用。

* **`keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment`**:
    * **作用**: 密钥用法扩展。定义了证书中包含的公钥可以用于哪些**基本**的密码学操作。
    * **`critical`**: 表示这个扩展是**关键（Critical）**的。任何处理此证书的应用程序**必须**理解并遵守 `keyUsage` 扩展的限制。如果应用程序不理解这个扩展，它必须拒绝该证书。
    * **`nonRepudiation` (不可否认性)**: 密钥可用于提供不可否认服务，即防止签名者事后否认其签名行为（通常用于数字签名合同、交易等场景，比单纯的 `digitalSignature` 含义更强）。
    * **`digitalSignature` (数字签名)**: 密钥可用于对数据进行签名（但不同于 `nonRepudiation`）。这是客户端认证中最核心的用法之一，客户端使用私钥签名数据以向服务器证明身份。
    * **`keyEncipherment` (密钥加密)**: 密钥可用于加密对称密钥，例如在 TLS 握手期间使用 RSA 密钥交换算法时，服务器用客户端公钥加密预主密钥。虽然现代 TLS 更多使用 ECDHE 等前向安全算法（此时客户端签名更重要），但包含此用法可支持旧协议或特定场景。

* **`extendedKeyUsage = clientAuth`**:
    * **作用**: 增强型密钥用法 (EKU)。定义了密钥可以用于哪些**特定应用场景**，是对 `keyUsage` 的补充。
    * **`clientAuth`**: 明确指出该证书可用于**客户端身份验证**（OID: 1.3.6.1.5.5.7.3.2）。这是客户端证书最主要的 EKU。

* **`# authorityInfoAccess = caIssuers;URI:http://demo.org/ca.html`** (注释掉):
    * **作用**: 授权信息访问 (AIA)。提供如何获取关于 CA 的附加信息（如颁发者证书）的位置。
    * `caIssuers`: 指明提供的是颁发者证书的获取方式。
    * `URI:http://...`: 指定了获取颁发者证书的 URL。
    * 因为前面有 `#`，所以此行当前**无效**。

* **`# certificatePolicies = 2.23.140.1.2.1,@policy_issuer_info`** (注释掉):
    * **作用**: 证书策略。指定与证书相关的特定策略标识符 (OID) 和可选的策略信息。用于表明证书的签发和使用遵循特定的规则集。
    * 此行当前**无效**。

* **`# authorityInfoAccess = OCSP;URI:http://ocsp.demo.org/`** (注释掉):
    * **作用**: 授权信息访问 (AIA)。此行用于提供在线证书状态协议 (OCSP) 响应器的位置。
    * `OCSP`: 指明提供的是 OCSP 服务的访问信息。
    * `URI:http://...`: 指定了 OCSP 服务器的 URL。
    * 此行当前**无效**。

---

### **`[ server_cert ]` 段落**

这个段落定义了专门用于**服务器证书**的扩展。服务器证书主要用于向客户端证明服务器的身份（例如，HTTPS 网站的证书）。

* **`keyUsage = critical, digitalSignature, keyEncipherment`**:
    * **作用**: 定义服务器公钥的基本用途。
    * **`critical`**: 同上，此扩展必须被处理。
    * **`digitalSignature`**: 对于使用如 ECDHE/DHE 的密钥交换算法的 TLS 握手至关重要，服务器需要用其私钥对握手参数进行签名。
    * **`keyEncipherment`**: 对于使用 RSA 密钥交换算法的 TLS 握手是必需的，客户端需要用服务器的公钥加密预主密钥。为了广泛兼容性，服务器证书通常包含此用法。

* **`extendedKeyUsage = serverAuth, clientAuth`**:
    * **作用**: 定义服务器密钥的特定应用场景。
    * **`serverAuth`**: 明确指出该证书可用于**服务器身份验证**（OID: 1.3.6.1.5.5.7.3.1）。这是服务器证书最主要的 EKU。
    * **`clientAuth`**: **有趣的是这里也包含了 `clientAuth`**。这意味着这个证书**不仅**可以用于认证服务器身份，**也**可以被该服务器用来作为客户端去认证到**其他**需要客户端认证的服务器。这在某些服务器到服务器的通信场景（如微服务间的 mTLS）可能有用，但更常见的做法是为服务器和客户端角色分别使用不同的证书。

* **`basicConstraints = CA:FALSE`**:
    * 同上，表明这个服务器证书**不是** CA 证书，不能用于签发其他证书。

* **`subjectKeyIdentifier = hash`**:
    * 同上，为此服务器证书的公钥生成一个标识符。

* **`authorityKeyIdentifier = keyid:always,issuer:always`**:
    * 同上，提供识别签发此服务器证书的 CA 的信息，帮助构建和验证信任链。
