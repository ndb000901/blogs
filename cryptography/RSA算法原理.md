# RSA算法原理

RSA（Rivest-Shamir-Adleman）是一种非对称加密算法，它基于大数因子分解的数学难题，被广泛用于数据加密和数字签名。

---

## 1. **RSA 加密算法流程**
RSA 的核心思想是利用两个大质数的乘积作为加密密钥，同时利用指数运算的特性来进行加密与解密。整个过程包括 **密钥生成、加密、解密** 三个主要步骤。

### **生成密钥**
RSA 需要生成公钥（public key）和私钥（private key）。

1. **选择两个大质数**  p, q ：
   - 选择两个随机的大质数  p  和  q （通常大于 1024 位）。
   - 计算它们的乘积：
   ```latex
     n = p * q
   ```

   n  用于公钥和私钥，是 RSA 密钥长度的主要决定因素。

2. **计算欧拉函数**：
    ```text
    φ(n) = (p-1) * (q-1)
    ```

   这里的 `φ(n)` 表示小于  n  且与  n  互质的整数个数。

3. **选择公钥指数  e **
   - 选择一个整数  e ，要求满足：
   ```text
   1 < e < φ(n), gcd(e, φ(n)) = 1
   ```

   - 常见选择：
     - ( e = 65537 )（ 2^16 + 1 ），因为它具有计算效率高、数学性质良好的特点。

4. **计算私钥指数  d **
   - 计算  d  使其满足：
   ```text
   e * d ≡ 1 (mod φ(n))
   ```
   - 也就是  d  是  e  关于模 φ(n)  的 **乘法逆元**，通常使用 **扩展欧几里得算法** 计算。

5. **公钥和私钥**
   - 公钥（用于加密）：
   ```text
   Public Key = (n, e)
   ```
   - 私钥（用于解密）：
   ```text
   Private Key} = (n, d)
   ```

---

## 2. **加密和解密过程**
### **2.1. 加密**
加密时，发送方使用 **接收方的公钥** 对明文  M  进行加密：
```text
C = M^e mod n
```

其中：
-  M  是明文（需要转换为数值）
-  C  是密文
-  e  和  n  来自公钥

### **2.2. 解密**
解密时，接收方使用 **自己的私钥** 计算：
```text
M = C^d mod n
```

根据 RSA 数学性质：
```text
(M^e)^d ≡ M^ed ≡ M mod n
```

其中  ed ≡ 1 mod φ(n) ，保证了加密和解密的可逆性。

---

## 3. **数字签名**
RSA 也可以用于数字签名，确保消息的完整性和身份验证。

### **3.1. 签名**
发送方使用 **自己的私钥** 对消息摘要（如 SHA-256）进行签名：

```text
S = H(M)^d mod n
```
其中  H(M)  是哈希函数的输出。

### **3.2. 验证**
接收方使用 **发送方的公钥** 验证：

```text
H(M) = S^e mod n
```

如果计算出的哈希值与原始消息的哈希值一致，则证明签名有效。

---

## 4. **安全性分析**
RSA 的安全性依赖于**大数因子分解难题**，即：

```text
n = p * q
```

若能快速分解  n ，就能计算出  φ(n)  并破解私钥  d 。

- **安全性依赖因素：**
  - 质数选取足够大（目前推荐 2048 位及以上）。
  - 使用随机大质数，避免选取特殊形态的质数。
  - 选择合适的加密指数  e ，一般采用 65537。

---

## 5.示例

### 生成RSA密钥

```bash

# openssl genrsa -out private.pem 2048

-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDI2n/AXGeh1GXE
9i1GaAqxUATpzG+54fBncCeRi05xrxcF9whICHPxetFOM/WsELB4nD4TZxCkrTGa
Acf6IvVEu1pxyFDbyvYQ75DnGPPWJvDO7TFXAQygE8rwC1sqrMa2YhcsjRr1sxL9
0D41okjUaekQ48l4Bpw73EzrmV88Nm3fKqWr02+GmjzZ+pbn6pVMWfqcw1fBU6zR
StvyY7HQiPw7D2uJY2moZjmQkj7d+cZKJYBPML5v8phDvrr3pQ24c5I8RU3KuCW/
cHXKLPQcxXvaLiZhAqxvP5VafDXkWfSKhJCAyx7dom/eEHvJMIqNR4acchHYYO2L
j9LLRnovAgMBAAECggEAH1SMpUYPY5wc4daKumxOmq5XjdwLY4HzEkIu5zzv2BDj
9P9okHVCbLJn8Bxvynlx+uZYUoYlqk0FKzedk47yFli+jiLH5iO93WdugXXldzbC
Im0NC2bJADaw1bUtG+Db6vrYZY/UhZX7ijxPr+W9buY0ak415jNAb0qjya5gv3nx
RJaDc4HLXrL3pEZaPz8/vlcp6JEj8NMV++vMIxA6Kqz+NEfUpRbcxCJsJFyUQxaN
Qn+kQZM6+IylWr9kW51dg21Y/sGRXpqlz9QQiA3vnfuQ7HRXSZ0+l68Zz0zHW0xT
cPs4DcFV1aKH3P2+y9whdzwrWdJPJKxEImVBQi+MYQKBgQDcK90BR/hoSNQrR3yq
j7MetnQf6mn5JsrfF3IPTJbLesLRr/f0jU8tugHj9rqYo3GnukwV4lx2ZMDc40MB
H9ILyP9BNICrDDSZ8uL9iWLJcSTHvtOcOoDHnAs+LTzcGSbXZqRDHEGME5/HWISa
Is86QhK1JrtpMBqKT+1LJMiU4QKBgQDpid9k0xxnSSru8hTZxxPdxM7Y1FaTwZHr
ZXgehH5ogodI268poctOoAj3SSW8SEJm8twBX2x7M6Az75I5OR13eI761mErhOmm
BdOkELKJB9gsgymqBpt3AmZKhUBJMXMpOkwZTDY+c9+e1HJhqoON+KsypeHGDYnk
ozP0AizhDwKBgB8VAipAIs12Gp3RhcP+bWcUkug+i59k3pcIFE4cnd00A3Texr57
hIYMEGBCYK1TxaPMJRmhzzPXnXKRktAB0gFf6sklxFRhcLubEniAgLW3h127jtwr
VirX2K56bHEWC+m0z9O5lXdU/eWClZW8/ExZL5LrWOHMrLKB/hnxo2+BAoGBALnH
802634KO4zeYntusEfK+yKoXKRR7zYAMhWUJk6HCDKr6EDs3OYu/pcJKj1lbxm8t
TbVN2vebVxtZvuIEvJs47c0/06cSIejwA7AllFGMSv0GDpPyk6Q15Xfszvoq+2jV
ton9Zb2C4mbwrFljkynSoyY0bgnm5UZcSk0XmeojAoGAQxO7DQ70pku6TTrpD9fG
yvhF1VkuQGZOvK2V+2GsdLmRnUNtFWMk8Bu+jf9bP2tudBbKT8LYmB9AY6ifXC4B
ligEPC6QGv9d6E4NcHq+XAn44JdRhiooeRiAWzH/FQKA4lX223xAjcWPvex4QbH6
TrXyT6ei9aDLXJRi+wrULoY=
-----END PRIVATE KEY-----
```

### 查看

- modulus（n）：公钥和私钥共享的模数。
- publicExponent（e）：通常是 65537（0x10001）。
- privateExponent（d）：私钥专有的指数。
- prime1（p）和 prime2（q）：生成 RSA 密钥的两个大素数。
- exponent1（dP）和 exponent2（dQ）：p 和 q 对应的私钥指数。
- coefficient（qInv）：q 模 p 的逆元，加速 RSA 解密运算。

```bash

# openssl rsa -in private.pem -text -noout

Private-Key: (2048 bit, 2 primes)
modulus:
    00:c8:da:7f:c0:5c:67:a1:d4:65:c4:f6:2d:46:68:
    0a:b1:50:04:e9:cc:6f:b9:e1:f0:67:70:27:91:8b:
    4e:71:af:17:05:f7:08:48:08:73:f1:7a:d1:4e:33:
    f5:ac:10:b0:78:9c:3e:13:67:10:a4:ad:31:9a:01:
    c7:fa:22:f5:44:bb:5a:71:c8:50:db:ca:f6:10:ef:
    90:e7:18:f3:d6:26:f0:ce:ed:31:57:01:0c:a0:13:
    ca:f0:0b:5b:2a:ac:c6:b6:62:17:2c:8d:1a:f5:b3:
    12:fd:d0:3e:35:a2:48:d4:69:e9:10:e3:c9:78:06:
    9c:3b:dc:4c:eb:99:5f:3c:36:6d:df:2a:a5:ab:d3:
    6f:86:9a:3c:d9:fa:96:e7:ea:95:4c:59:fa:9c:c3:
    57:c1:53:ac:d1:4a:db:f2:63:b1:d0:88:fc:3b:0f:
    6b:89:63:69:a8:66:39:90:92:3e:dd:f9:c6:4a:25:
    80:4f:30:be:6f:f2:98:43:be:ba:f7:a5:0d:b8:73:
    92:3c:45:4d:ca:b8:25:bf:70:75:ca:2c:f4:1c:c5:
    7b:da:2e:26:61:02:ac:6f:3f:95:5a:7c:35:e4:59:
    f4:8a:84:90:80:cb:1e:dd:a2:6f:de:10:7b:c9:30:
    8a:8d:47:86:9c:72:11:d8:60:ed:8b:8f:d2:cb:46:
    7a:2f
publicExponent: 65537 (0x10001)
privateExponent:
    1f:54:8c:a5:46:0f:63:9c:1c:e1:d6:8a:ba:6c:4e:
    9a:ae:57:8d:dc:0b:63:81:f3:12:42:2e:e7:3c:ef:
    d8:10:e3:f4:ff:68:90:75:42:6c:b2:67:f0:1c:6f:
    ca:79:71:fa:e6:58:52:86:25:aa:4d:05:2b:37:9d:
    93:8e:f2:16:58:be:8e:22:c7:e6:23:bd:dd:67:6e:
    81:75:e5:77:36:c2:22:6d:0d:0b:66:c9:00:36:b0:
    d5:b5:2d:1b:e0:db:ea:fa:d8:65:8f:d4:85:95:fb:
    8a:3c:4f:af:e5:bd:6e:e6:34:6a:4e:35:e6:33:40:
    6f:4a:a3:c9:ae:60:bf:79:f1:44:96:83:73:81:cb:
    5e:b2:f7:a4:46:5a:3f:3f:3f:be:57:29:e8:91:23:
    f0:d3:15:fb:eb:cc:23:10:3a:2a:ac:fe:34:47:d4:
    a5:16:dc:c4:22:6c:24:5c:94:43:16:8d:42:7f:a4:
    41:93:3a:f8:8c:a5:5a:bf:64:5b:9d:5d:83:6d:58:
    fe:c1:91:5e:9a:a5:cf:d4:10:88:0d:ef:9d:fb:90:
    ec:74:57:49:9d:3e:97:af:19:cf:4c:c7:5b:4c:53:
    70:fb:38:0d:c1:55:d5:a2:87:dc:fd:be:cb:dc:21:
    77:3c:2b:59:d2:4f:24:ac:44:22:65:41:42:2f:8c:
    61
prime1:
    00:dc:2b:dd:01:47:f8:68:48:d4:2b:47:7c:aa:8f:
    b3:1e:b6:74:1f:ea:69:f9:26:ca:df:17:72:0f:4c:
    96:cb:7a:c2:d1:af:f7:f4:8d:4f:2d:ba:01:e3:f6:
    ba:98:a3:71:a7:ba:4c:15:e2:5c:76:64:c0:dc:e3:
    43:01:1f:d2:0b:c8:ff:41:34:80:ab:0c:34:99:f2:
    e2:fd:89:62:c9:71:24:c7:be:d3:9c:3a:80:c7:9c:
    0b:3e:2d:3c:dc:19:26:d7:66:a4:43:1c:41:8c:13:
    9f:c7:58:84:9a:22:cf:3a:42:12:b5:26:bb:69:30:
    1a:8a:4f:ed:4b:24:c8:94:e1
prime2:
    00:e9:89:df:64:d3:1c:67:49:2a:ee:f2:14:d9:c7:
    13:dd:c4:ce:d8:d4:56:93:c1:91:eb:65:78:1e:84:
    7e:68:82:87:48:db:af:29:a1:cb:4e:a0:08:f7:49:
    25:bc:48:42:66:f2:dc:01:5f:6c:7b:33:a0:33:ef:
    92:39:39:1d:77:78:8e:fa:d6:61:2b:84:e9:a6:05:
    d3:a4:10:b2:89:07:d8:2c:83:29:aa:06:9b:77:02:
    66:4a:85:40:49:31:73:29:3a:4c:19:4c:36:3e:73:
    df:9e:d4:72:61:aa:83:8d:f8:ab:32:a5:e1:c6:0d:
    89:e4:a3:33:f4:02:2c:e1:0f
exponent1:
    1f:15:02:2a:40:22:cd:76:1a:9d:d1:85:c3:fe:6d:
    67:14:92:e8:3e:8b:9f:64:de:97:08:14:4e:1c:9d:
    dd:34:03:74:de:c6:be:7b:84:86:0c:10:60:42:60:
    ad:53:c5:a3:cc:25:19:a1:cf:33:d7:9d:72:91:92:
    d0:01:d2:01:5f:ea:c9:25:c4:54:61:70:bb:9b:12:
    78:80:80:b5:b7:87:5d:bb:8e:dc:2b:56:2a:d7:d8:
    ae:7a:6c:71:16:0b:e9:b4:cf:d3:b9:95:77:54:fd:
    e5:82:95:95:bc:fc:4c:59:2f:92:eb:58:e1:cc:ac:
    b2:81:fe:19:f1:a3:6f:81
exponent2:
    00:b9:c7:f3:4d:ba:df:82:8e:e3:37:98:9e:db:ac:
    11:f2:be:c8:aa:17:29:14:7b:cd:80:0c:85:65:09:
    93:a1:c2:0c:aa:fa:10:3b:37:39:8b:bf:a5:c2:4a:
    8f:59:5b:c6:6f:2d:4d:b5:4d:da:f7:9b:57:1b:59:
    be:e2:04:bc:9b:38:ed:cd:3f:d3:a7:12:21:e8:f0:
    03:b0:25:94:51:8c:4a:fd:06:0e:93:f2:93:a4:35:
    e5:77:ec:ce:fa:2a:fb:68:d5:b6:89:fd:65:bd:82:
    e2:66:f0:ac:59:63:93:29:d2:a3:26:34:6e:09:e6:
    e5:46:5c:4a:4d:17:99:ea:23
coefficient:
    43:13:bb:0d:0e:f4:a6:4b:ba:4d:3a:e9:0f:d7:c6:
    ca:f8:45:d5:59:2e:40:66:4e:bc:ad:95:fb:61:ac:
    74:b9:91:9d:43:6d:15:63:24:f0:1b:be:8d:ff:5b:
    3f:6b:6e:74:16:ca:4f:c2:d8:98:1f:40:63:a8:9f:
    5c:2e:01:96:28:04:3c:2e:90:1a:ff:5d:e8:4e:0d:
    70:7a:be:5c:09:f8:e0:97:51:86:2a:28:79:18:80:
    5b:31:ff:15:02:80:e2:55:f6:db:7c:40:8d:c5:8f:
    bd:ec:78:41:b1:fa:4e:b5:f2:4f:a7:a2:f5:a0:cb:
    5c:94:62:fb:0a:d4:2e:86
```


