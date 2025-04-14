# Mysql函数-InformationFunctions

在 MySQL 8 中，**Information Functions（信息函数）** 主要用于获取当前会话、连接、用户、数据库、服务器状态等信息，常用于调试、审计、安全控制、运维监控等场景。


## 1. 会话与连接信息函数

### 1.1 `CONNECTION_ID()`
> 获取当前客户端连接的唯一 ID。

```sql
SELECT CONNECTION_ID();  -- 如 12457
```

可用于 `KILL CONNECTION_ID()` 来断开连接。

---

### 1.2 `CURRENT_USER()`
> 返回数据库服务器识别的 **已认证用户**（格式为 `user@host`）。

```sql
SELECT CURRENT_USER();  -- 'reader@localhost'
```

与 `USER()` 不同，它是真正登录成功的身份（不是授权时可能临时伪装的用户）。

---

### 1.3 `USER()` 或 `SESSION_USER()`
> 返回客户端连接请求中的用户（可能与 CURRENT_USER() 不一致）。

```sql
SELECT USER();  -- 'dev@localhost'
```

区别：
- `USER()`：你连接时提供的用户名
- `CURRENT_USER()`：MySQL 验证通过后的账号

---

## 2. 数据库与表相关

### 2.1 `DATABASE()`
> 返回当前默认数据库名。

```sql
SELECT DATABASE();  -- 'testdb'
```

如果没有 `USE dbname`，则返回 NULL。

---

### 2.2 `SCHEMA()`
> `SCHEMA()` 是 `DATABASE()` 的别名。

```sql
SELECT SCHEMA();  -- 等价于 SELECT DATABASE();
```

---

## 3. 服务器信息函数

### 3.1 `VERSION()`
> 返回 MySQL 的版本字符串。

```sql
SELECT VERSION();  -- '8.0.36'
```

---

### 3.2 `@@VERSION_COMMENT`
> 返回服务器构建信息说明。

```sql
SELECT @@version_comment;
-- 'MySQL Community Server - GPL'
```

---

### 3.3 `@@hostname`
> 获取服务器主机名。

```sql
SELECT @@hostname;
```

---

## 4. 字符集与校对规则

### 4.1 `CHARSET(str)`
> 返回字符串 `str` 使用的字符集名称。

```sql
SELECT CHARSET('你好');  -- utf8mb4
```

---

### 4.2 `COLLATION(str)`
> 返回字符串的排序规则。

```sql
SELECT COLLATION('你好');  -- utf8mb4_general_ci
```

---

## 5. 用户权限信息函数

### 5.1 `CURRENT_ROLE()`
> 返回当前用户激活的角色列表（MySQL 8 新特性）。

```sql
SELECT CURRENT_ROLE();
-- 'app_read, app_write'
```

---

### 5.2 `CURRENT_SCHEMA()`
> 返回当前使用的 schema（等价于 `DATABASE()`）

---

## 6. 判断锁

### 6.1 `IS_USED_LOCK('lock_name')`
> 判断某个命名锁是否被使用中，返回连接ID或 NULL。

```sql
SELECT IS_USED_LOCK('my_lock');  -- NULL 或连接ID
```

---

### 6.2 `IS_FREE_LOCK('lock_name')`
> 判断命名锁是否空闲。

```sql
SELECT IS_FREE_LOCK('my_lock');  -- 1 空闲，0 被占用
```

---

## 7. 其他常用信息函数

### 7.1 `LAST_INSERT_ID()`
> 返回当前 session 内，最近一次 `AUTO_INCREMENT` 插入的值。

```sql
INSERT INTO users(name) VALUES ('Tom');
SELECT LAST_INSERT_ID();  -- 返回刚插入的 id
```

---

### 7.2 `ROW_COUNT()`
> 返回上一条 SQL 影响的行数。

```sql
UPDATE users SET name='Jim' WHERE id=5;
SELECT ROW_COUNT();  -- 1 表示影响了 1 行
```

---
