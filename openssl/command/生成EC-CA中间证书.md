# 生成EC CA中间证书

## 1.初始化

工作目录：`/home/hello/code/openssl/demo/ec-ca-demo/l2-dir`

```bash

export CA_L2_DIR=/home/hello/code/openssl/demo/ec-ca-demo/l2-dir
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

## 2.生成CA 中间证书 EC密钥

```bash

export CA_L2_KEY_PATH=$CA_L2_DIR/key.pem

# 生成EC密钥(prime256v1)
openssl ecparam -name prime256v1 -genkey -noout -out $CA_L2_KEY_PATH

# 生成EC密钥secp521r1
openssl ecparam -name secp521r1 -genkey -noout -out $CA_L2_KEY_PATH

# 生成EC密钥ed25519
openssl genpkey -algorithm ed25519 -out $CA_L2_KEY_PATH

# 加密
openssl ec -aes256 -in $CA_L2_KEY_PATH -out $CA_L2_KEY_PATH
```

[加密参考](./EC密钥管理.md)

## 3.生成签名请求配置

```bash

export CA_L2_REQ_PATH=$CA_L2_DIR/ca.csr.cnf

cat > $CA_L2_REQ_PATH << EOL
[ req ]
default_bits        = 4096
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
commonName                      = Hello CA EC E1
EOL
```

## 4.生成签名请求

需要使用私钥对签名请求进行签名
```bash

export CA_L2_CSR_PATH=$CA_L2_DIR/ca.csr.pem

openssl req -config $CA_L2_REQ_PATH -new -key $CA_L2_KEY_PATH -out $CA_L2_CSR_PATH
```


### 查看签名请求

```bash

openssl req -in $CA_L2_CSR_PATH -noout -text
```

## 5.生成CA中间证书

```bash

export CA_L2_CERT_PATH=$CA_L2_DIR/ca.pem

# 10 年
openssl ca \
    -config $CA_ROOT_DIR/ca.cnf \
    -extensions v3_intermediate_ca \
    -days 3650 \
    -notext \
    -md sha384 \
    -batch \
    -in $CA_L2_CSR_PATH \
    -out $CA_L2_CERT_PATH
```

### 查看证书信息

```bash

openssl x509 -noout -text -in $CA_L2_CERT_PATH
```

### 验证

使用根证书验证

```bash

openssl verify -CAfile $CA_ROOT_CERT_PATH $CA_L2_CERT_PATH
```

## 6.生成证书链文件

```bash

export CA_L2_CERT_CHAIN_PATH=$CA_L2_DIR/ca.fullchain.pem

cat > $CA_L2_CERT_CHAIN_PATH << EOL
$(cat $CA_L2_CERT_PATH)

$(cat $CA_ROOT_CERT_PATH)
EOL
```

## 7.配置CA

```bash

export CA_L2_CONF_PATH=$CA_L2_DIR/ca.cnf

cat > $CA_L2_CONF_PATH << EOL
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

