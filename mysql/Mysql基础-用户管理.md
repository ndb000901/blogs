# Mysql基础-用户管理

## 1. 创建用户

```sql
CREATE USER '用户名'@'主机' IDENTIFIED BY '密码';
```

### 示例：

```sql
CREATE USER 'app_user'@'%' IDENTIFIED BY 'StrongP@ss123!';
```

- `'%'` 表示任意主机（可指定 `localhost` 或 IP）
- MySQL 8 默认使用 `caching_sha2_password` 加密方式（更安全）

### 可选加密方式：

```sql
CREATE USER 'user'@'localhost'
IDENTIFIED WITH mysql_native_password BY '123456';
```

---

## 2. 查看用户

### 查看所有用户

```sql
SELECT user, host FROM mysql.user;
```

### 查看某用户的权限

```sql
SHOW GRANTS FOR 'app_user'@'%';
```

---

## 3. 修改用户

### 修改用户名

```sql
RENAME USER 'old_user'@'%' TO 'new_user'@'%';
```

### 修改密码

```sql
ALTER USER 'user1'@'%' IDENTIFIED BY 'newpassword!';
```

也可以显式指定加密插件：

```sql
ALTER USER 'user1'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
```

---

## 4. 删除用户

```sql
DROP USER '用户名'@'主机';
```

### 示例：

```sql
DROP USER 'app_user'@'%';
```

---

## 5. 密码策略设置（MySQL 8 新特性）

可通过插件 `validate_password` 或 `caching_sha2_password` 设置密码复杂度要求。

### 查看密码策略配置

```sql
SHOW VARIABLES LIKE 'validate_password%';
```

### 设置密码策略（参数可加入 `my.cnf`）

```sql
SET GLOBAL validate_password.length = 10;
SET GLOBAL validate_password.mixed_case_count = 1;
SET GLOBAL validate_password.number_count = 1;
SET GLOBAL validate_password.special_char_count = 1;
```

---

## 6. 账户锁定与解锁

用于禁用用户登录：

```sql
ALTER USER 'user1'@'%' ACCOUNT LOCK;
ALTER USER 'user1'@'%' ACCOUNT UNLOCK;
```

---

## 7. 强制用户修改密码

登录后必须修改：

```sql
ALTER USER 'user1'@'%' PASSWORD EXPIRE;
```

或者设置过期时间（单位为天）：

```sql
ALTER USER 'user1'@'%' PASSWORD EXPIRE INTERVAL 30 DAY;
```

---

## 8. 指定资源限制（限速/限连接）

```sql
CREATE USER 'limited_user'@'%'
IDENTIFIED BY '123456'
WITH MAX_QUERIES_PER_HOUR 100
     MAX_UPDATES_PER_HOUR 20
     MAX_CONNECTIONS_PER_HOUR 10
     MAX_USER_CONNECTIONS 5;
```

查看限制字段：

```sql
SELECT * FROM mysql.user WHERE user = 'limited_user';
```

---

## 9. 查看当前登录用户

```sql
SELECT CURRENT_USER();     -- 当前权限检查身份
SELECT USER();             -- 登录身份（可能包含代理情况）
```

---

## 10. 查看用户密码过期/锁定状态（MySQL 8 专属）

```sql
SELECT user, host, account_locked, password_expired
FROM mysql.user
WHERE user = 'user1';
```

---

## 11. 用户权限迁移技巧

当你需要迁移用户权限到新用户：

```sql
-- 查看原权限
SHOW GRANTS FOR 'old_user'@'%';

-- 将权限复制到新用户
GRANT ... TO 'new_user'@'%';
```




