# Mysql基础-权限管理


## 1. MySQL 权限体系概览

MySQL 权限从粒度上分为以下层级：

| 层级         | 示例                        |
|--------------|-----------------------------|
| 全局         | `ON *.*`（所有数据库所有表） |
| 数据库级     | `ON db_name.*`              |
| 表级         | `ON db_name.table_name`     |
| 列级         | `ON db_name.table (col1)`   |
| 存储过程级   | `ON PROCEDURE db.proc_name` |

---

## 2. 常见权限列表

| 权限             | 描述                                 |
|------------------|--------------------------------------|
| `ALL PRIVILEGES` | 所有权限                             |
| `SELECT`         | 查询数据                             |
| `INSERT`         | 插入数据                             |
| `UPDATE`         | 修改数据                             |
| `DELETE`         | 删除数据                             |
| `CREATE`         | 创建数据库、表、索引等               |
| `DROP`           | 删除数据库、表                       |
| `ALTER`          | 修改表结构                           |
| `INDEX`          | 创建/删除索引                        |
| `EXECUTE`        | 执行存储过程/函数                    |
| `USAGE`          | 无特权，仅代表能登录（默认创建用户） |

查看全部权限（官方文档也可查）：

```sql
SHOW PRIVILEGES;
```

---

## 3. 用户管理

### 3.1 创建用户

```sql
CREATE USER 'user1'@'%' IDENTIFIED BY 'password';
```

- `%`：表示任意主机可连接（生产建议使用精确 IP）
- 密码加密方式默认使用 `caching_sha2_password`（MySQL 8 新默认）

### 3.2 修改密码

```sql
ALTER USER 'user1'@'%' IDENTIFIED BY 'newpassword';
```

### 3.3 删除用户

```sql
DROP USER 'user1'@'%';
```

---

## 4. 授权操作（GRANT）

### 4.1 授权语法

```sql
GRANT privileges
ON database.table
TO 'user'@'host'
[WITH GRANT OPTION];
```

### 4.2 示例：授权某用户访问某数据库所有表

```sql
GRANT SELECT, INSERT, UPDATE
ON testdb.*
TO 'user1'@'%';
```

### 4.3 授予所有权限

```sql
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;
```

- `WITH GRANT OPTION`：允许该用户再授权他人

---

## 5. 回收权限（REVOKE）

```sql
REVOKE privilege_list
ON db_name.table_name
FROM 'user'@'host';
```

示例：

```sql
REVOKE UPDATE ON testdb.* FROM 'user1'@'%';
```

---

## 6. 查看权限

### 当前用户拥有的权限

```sql
SHOW GRANTS FOR CURRENT_USER();
```

或指定用户：

```sql
SHOW GRANTS FOR 'user1'@'%';
```

---

## 7. 角色管理（MySQL 8 新特性）

### 7.1 创建角色

```sql
CREATE ROLE 'readonly_role';
```

### 7.2 给角色授权

```sql
GRANT SELECT ON db1.* TO 'readonly_role';
```

### 7.3 将角色赋予用户

```sql
GRANT 'readonly_role' TO 'user1'@'%';
```

### 7.4 设置默认角色（用户登录自动启用）

```sql
SET DEFAULT ROLE 'readonly_role' TO 'user1'@'%';
```

### 7.5 启用角色（会话级）

```sql
SET ROLE 'readonly_role';
```

### 7.6 查看角色权限

```sql
SHOW GRANTS FOR 'readonly_role';
```

---

## 8. 刷新权限

修改权限后，MySQL 8 通常自动生效，但可手动刷新：

```sql
FLUSH PRIVILEGES;
```

---

## 9. 安全加固建议

| 项目                       | 建议说明                                               |
|----------------------------|--------------------------------------------------------|
| 避免使用 `root` 连接应用  | 创建最小权限业务账号                                   |
| 拆分读写账户               | 只赋予读写必要权限，配合角色机制更清晰                |
| 限制登录主机               | 用户使用 `'user'@'192.168.1.100'` 精确控制来源 IP       |
| 定期审计权限               | 定期跑 `SHOW GRANTS` 脚本，输出权限变更记录            |
| 禁止空口令                 | MySQL 8 默认已禁止                                      |
| 使用 TLS 加密连接          | `REQUIRE SSL` 可强制账号使用 TLS 安全连接               |

---

## 10. 示例

```sql
-- 创建用户
CREATE USER 'app_user'@'10.0.0.%' IDENTIFIED BY 'app_pass';

-- 授权 SELECT、INSERT 权限
GRANT SELECT, INSERT ON app_db.* TO 'app_user'@'10.0.0.%';

-- 创建只读角色并赋给用户
CREATE ROLE 'reader';
GRANT SELECT ON app_db.* TO 'reader';
GRANT 'reader' TO 'analyst'@'%';

-- 查看用户权限
SHOW GRANTS FOR 'app_user'@'10.0.0.%';
```

---

