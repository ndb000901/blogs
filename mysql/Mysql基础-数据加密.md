# Mysql基础-数据加密

## 1. MySQL 支持的加密机制概览

| 加密类型     | 说明                                     | MySQL 版本 | 是否内置 |
|--------------|------------------------------------------|------------|---|
| TLS 传输加密 | 加密客户端与服务器之间的连接通信         | MySQL 5.7+ | 是 |
| 数据静态加密 | 表空间加密（InnoDB TDE）、redo/undo 加密 | MySQL 8.0+ | 是  |
| 加密函数     | `AES_ENCRYPT()`、`SHA2()` 等              | MySQL 5.7+ | 是  |
| 插件加密     | 使用 Keyring 插件存储密钥                 | MySQL 5.7+ | 是  |

---

## 2. 传输加密（TLS）

### 2.1 启用 TLS 加密

编辑 `my.cnf`（服务端）：

```ini
[mysqld]
ssl-ca      = /etc/mysql/ssl/ca.pem
ssl-cert    = /etc/mysql/ssl/server-cert.pem
ssl-key     = /etc/mysql/ssl/server-key.pem
```

> 可使用 OpenSSL 自签证书，也支持 Let’s Encrypt。

### 2.2 创建使用 SSL 的用户

```sql
CREATE USER 'secure_user'@'%' IDENTIFIED BY '123456' REQUIRE SSL;
```

或者更细粒度限制：

```sql
CREATE USER 'secure_user'@'%' IDENTIFIED BY '123456'
REQUIRE SUBJECT '/CN=mysql-client' AND ISSUER '/CN=mysql-ca';
```

### 2.3 客户端连接（强制加密）

```bash

mysql -u secure_user -p --ssl-ca=ca.pem --ssl-cert=client-cert.pem --ssl-key=client-key.pem
```

---

## 3. 静态加密（TDE：Transparent Data Encryption）

### 3.1 表空间加密（InnoDB）

从 MySQL 8.0 开始，InnoDB 原生支持透明加密。

```sql
CREATE TABLE t1 (
  id INT,
  secret_data VARCHAR(100)
) ENCRYPTION='Y';
```

### 3.2 开启全局默认表加密

```sql
SET GLOBAL innodb_encrypt_tables = ON;
SET GLOBAL innodb_encrypt_log = ON;
```

### 3.3 查看加密信息

```sql
SELECT * FROM information_schema.INNODB_TABLESPACES_ENCRYPTION;
```

---

## 4. Keyring 插件（密钥管理）

### 4.1 支持的插件

| 插件名称              | 说明                           |
|-----------------------|--------------------------------|
| `keyring_file`        | 使用本地文件保存密钥           |
| `keyring_encrypted`   | 加密保存，推荐                 |
| `keyring_hashicorp`   | 企业级，接入 Vault 密钥管理    |
| `keyring_aws`         | AWS KMS                        |

### 4.2 启用 `keyring_file`

编辑配置文件 `my.cnf`：

```ini
[mysqld]
early-plugin-load=keyring_file.so
keyring_file_data=/var/lib/mysql-keyring/keyring
```

重启后：

```sql
SHOW PLUGINS;
```

> 必须在启动阶段加载。

---

## 5. 加密函数（适合字段级别加密）

### AES 加密解密

```sql
-- 加密
SELECT HEX(AES_ENCRYPT('秘密数据', '密钥'));

-- 解密
SELECT AES_DECRYPT(UNHEX('...'), '密钥');
```

> 建议使用 256-bit key，MySQL 8.0 默认采用 AES-256-CBC。

### 哈希函数

```sql
SELECT SHA2('mypassword', 256);  -- SHA-256
SELECT MD5('mypassword');        -- 不推荐，已过时
```

### 随机数生成

```sql
SELECT RANDOM_BYTES(16);  -- 生成 16 字节随机数
```

---

## 6. 日志与临时文件加密（InnoDB）

MySQL 8 支持 redo log、undo log、临时表空间加密：

```ini
[mysqld]
innodb_redo_log_encrypt=ON
innodb_undo_log_encrypt=ON
innodb_temp_tablespace_encrypt=ON
```

---

## 7. 加密策略

| 加密项目       | 建议                                             |
|----------------|--------------------------------------------------|
| 传输加密       | 一律启用 SSL/TLS + 证书验证                      |
| 表空间加密     | 开启 InnoDB 表空间加密，确保磁盘数据安全         |
| 密钥管理       | 使用 `keyring_encrypted` 或外部密钥管理系统      |
| 字段加密       | 重要字段使用 `AES_ENCRYPT()`，密钥安全存储       |
| 审计日志保护   | 启用日志加密，防止信息泄露                      |
| 临时文件加密   | 防止敏感信息落入中间 tmp 文件                    |

---

## 8. 常见问题

### 是否影响性能？
> 表空间加密对性能影响较小（<5%），传输加密在连接密集时略有影响。

### 如何防止密钥泄露？
> 密钥绝不硬编码，可集成 HashiCorp Vault、KMS 等安全方案。

### 能否对已有表加密？
```sql
ALTER TABLE t1 ENCRYPTION='Y';
```

---

