# 生成EC CA 根证书

## 1.初始化

工作目录：`/home/hello/code/openssl/demo/ec-ca-demo/root-dir`

```bash

export CA_ROOT_DIR=/home/hello/code/openssl/demo/ec-ca-demo/root-dir
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

## 2.生成CA根证书 EC密钥

```bash

export CA_ROOT_KEY_PATH=$CA_ROOT_DIR/key.pem

# 生成EC密钥(prime256v1)
openssl ecparam -name prime256v1 -genkey -noout -out $CA_ROOT_KEY_PATH

# 生成EC密钥secp521r1
openssl ecparam -name secp521r1 -genkey -noout -out $CA_ROOT_KEY_PATH

# 生成EC密钥ed25519
openssl genpkey -algorithm ed25519 -out $CA_ROOT_KEY_PATH

# 加密
openssl ec -aes256 -in $CA_ROOT_KEY_PATH -out $CA_ROOT_KEY_PATH

```

[加密参考](./EC密钥管理.md)

## 3.生成签名请求（CSR）配置文件

```bash

CA_ROOT_REQ_CONFIG_PATH=$CA_ROOT_DIR/ca.csr.cnf

cat > $CA_ROOT_REQ_CONFIG_PATH << EOL
[ req ]

default_bits        = 4096
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
commonName                      = Hello CA EC Root

[ v3_ca ]

subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign
EOL
```

## 4.生成CA根证书

```bash

CA_ROOT_CERT_PATH=$CA_ROOT_DIR/ca.pem

# 生成一个有效期为 100 年的自签名 CA 根证书
openssl req -config $CA_ROOT_REQ_CONFIG_PATH \
    -new \
    -x509 \
    -days 36500 \
    -sha256 \
    -extensions v3_ca \
    -key $CA_ROOT_KEY_PATH \
    -out $CA_ROOT_CERT_PATH
```

注意：
`-sha256` 对ed-25519 无效： Ed25519 签名算法在其标准定义中，强制性地、紧密地集成了一个特定的哈希函数（SHA-512），并且其签名过程内部就包含了对消息进行哈希的步骤

### 查看证书信息

```bash

openssl x509 -noout -text -in $CA_ROOT_CERT_PATH
```

### 验证

```bash

openssl verify -CAfile $CA_ROOT_CERT_PATH $CA_ROOT_CERT_PATH
```

## 5.配置CA

```bash

CA_ROOT_CONF_PATH=$CA_ROOT_DIR/ca.cnf

cat > $CA_ROOT_CONF_PATH << EOL
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

# SHA-1 is deprecated, so use SHA-2 instead.
default_md        = sha256

name_opt          = ca_default
cert_opt          = ca_default
default_days      = 375
preserve          = no
policy            = policy_strict
copy_extensions   = copy

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
commonName                      = Hello CA EC Root

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

[ ocsp ]
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer:always
keyUsage = critical, digitalSignature
extendedKeyUsage = critical, OCSPSigning
EOL
```
