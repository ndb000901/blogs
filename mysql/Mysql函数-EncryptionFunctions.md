# Mysql函数-EncryptionFunctions

## 1. 常用加密函数

| 类别       | 函数名                          | 说明                          |
|------------|----------------------------------|-------------------------------|
| 对称加密   | `AES_ENCRYPT()`, `AES_DECRYPT()`| 使用对称密钥加解密（推荐 AES-256-GCM） |
| 散列       | `MD5()`, `SHA1()`, `SHA2()`     | 不可逆的哈希函数              |
| 随机字符串 | `RANDOM_BYTES()`                | 生成随机二进制字节            |
| Base64     | `TO_BASE64()`, `FROM_BASE64()`  | 编解码 Base64 字符串          |
| 密钥管理   | `CREATE_SYMMETRIC_DERIVED_KEY()`| 衍生密钥（支持 MySQL 8+）     |

---

## 2. 对称加密函数

### 2.1 `AES_ENCRYPT(str, key [, mode [, iv]])`
> 使用 AES 算法加密数据。推荐使用 `AES-256-GCM` 模式 + IV。

```sql
-- 加密字符串，结果为 BINARY（二进制）
SELECT TO_BASE64(AES_ENCRYPT('HelloWorld', 'mySecretKey'));
```

### 2.2 `AES_DECRYPT(crypt_str, key [, mode [, iv]])`
> 将二进制密文解密为明文

```sql
-- 解密 Base64 后的密文
SELECT AES_DECRYPT(FROM_BASE64('...'), 'mySecretKey');
```

#### 推荐使用 GCM 模式示例（MySQL 8.0+）

```sql
SET @key = 'MyStrongKey1234567890123456';  -- 256-bit 密钥
SET @iv = RANDOM_BYTES(12);                -- 96-bit IV

-- 加密
SET @enc = AES_ENCRYPT('TopSecret', @key, 'aes-256-gcm', @iv);

-- 解密
SELECT AES_DECRYPT(@enc, @key, 'aes-256-gcm', @iv);
```

> **注意**：
> - `aes-256-ecb` 也可以用，但不安全（无 IV，容易被攻击）
> - `aes-256-gcm` 支持认证标签，更安全

---

## 3. 散列函数（不可逆）

### 3.1 `MD5(str)`
- 生成 32 位的十六进制字符串，不可逆
```sql
SELECT MD5('password');  -- e.g. 5f4dcc3b5aa765d61d8327deb882cf99
```

### 3.2 `SHA1(str)`
- 生成 40 位十六进制 SHA-1 摘要
```sql
SELECT SHA1('password');
```

### 3.3 `SHA2(str, hash_length)`
- 可选 hash 长度：`224`, `256`, `384`, `512`

```sql
SELECT SHA2('password', 256); -- 推荐使用 SHA2 替代 MD5 / SHA1
```

---

## 4. 随机字节生成

### `RANDOM_BYTES(n)`
> 返回长度为 `n` 的随机字节（`BINARY` 类型）

```sql
SELECT HEX(RANDOM_BYTES(16)); -- 生成 16 字节的随机值（常用于密钥或 IV）
```

---

## 5. Base64 编解码

### 5.1 `TO_BASE64(str)`
- 把字符串或二进制转为 Base64 编码（ASCII 格式）

```sql
SELECT TO_BASE64('Hello'); -- SGVsbG8=
```

### 5.2 `FROM_BASE64(str)`
- 解码 Base64 编码回原始值（`BLOB`）

```sql
SELECT FROM_BASE64('SGVsbG8='); -- 'Hello'
```

---

## 6. 密钥衍生函数（MySQL 8.0.30+）

### `CREATE_SYMMETRIC_DERIVED_KEY()`
- 从口令派生出 AES 加密密钥
- 更适合密钥管理场景（使用 PBKDF2）

```sql
SET @key = CREATE_SYMMETRIC_DERIVED_KEY('password', 'salt', 10000, 'aes-256-gcm');
```

---



