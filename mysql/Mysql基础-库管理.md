# Mysql基础-库管理


## 1. 库的基本操作

### 1.1 创建数据库

```sql
CREATE DATABASE db_name;
```

常用扩展：

```sql
CREATE DATABASE db_name
  [DEFAULT CHARACTER SET utf8mb4]
  [DEFAULT COLLATE utf8mb4_general_ci];

CREATE DATABASE db_name
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_general_ci;
```


推荐字符集为 `utf8mb4`，防止 emoji、特殊字符存储出错。  
排序规则推荐 `utf8mb4_general_ci`（不区分大小写）或 `utf8mb4_bin`（区分大小写）。

---

### 1.2 查看已有数据库

```sql
SHOW DATABASES;
```

可以加过滤：

```sql
SHOW DATABASES LIKE 'test%';
```

---

### 1.3 查看当前使用的数据库

```sql
SELECT DATABASE();
```

---

### 1.4 使用数据库

```sql
USE db_name;
```

---

### 1.5 删除数据库（危险操作）

```sql
DROP DATABASE db_name;
```

一旦执行，所有数据 & 表结构将被删除，**无法恢复**。

**建议加前置判断：**

```sql
DROP DATABASE IF EXISTS db_name;
```

---

## 2. 库级权限管理

### 2.1 授权用户操作某库

```sql
GRANT ALL PRIVILEGES ON db_name.* TO 'username'@'host';
```

指定权限粒度更细：

```sql
GRANT SELECT, INSERT, UPDATE ON db_name.* TO 'username'@'host';
```

常用权限包括：

| 权限名称           | 解释 (针对数据库操作)                                                                                                                                                                                                                                                           |
|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ALL PRIVILEGES`   | 授予用户对指定数据库的所有权限，包括创建、删除、查询、修改等所有操作。通常只授予超级管理员。                                                                                                                                                                                              |
| `CREATE`           | 允许用户在指定的数据库中创建新的表、视图、存储过程、存储函数、触发器和事件。                                                                                                                                                                                                            |
| `DROP`             | 允许用户删除指定的数据库以及该数据库中的所有对象（表、视图、存储过程等）。这是一个高风险权限，应谨慎授予。                                                                                                                                                                                          |
| `ALTER`            | 允许用户修改指定数据库中现有表的结构，包括添加、删除或修改列，更改数据类型，添加或删除约束，以及重命名表等操作。                                                                                                                                                                                          |
| `INDEX`            | 允许用户在指定数据库的表中创建和删除索引，以提高查询性能。                                                                                                                                                                                                                            |
| `SELECT`           | 允许用户查询（读取）指定数据库中表或视图的数据。这是访问数据库最基本的权限。                                                                                                                                                                                                              |
| `INSERT`           | 允许用户向指定数据库的表中插入新的数据行。                                                                                                                                                                                                                                            |
| `UPDATE`           | 允许用户修改（更新）指定数据库中现有表的数据行。                                                                                                                                                                                                                                            |
| `DELETE`           | 允许用户从指定数据库的表中删除数据行。                                                                                                                                                                                                                                            |
| `EXECUTE`          | 允许用户执行指定数据库中的存储过程和存储函数。                                                                                                                                                                                                                                            |



---

### 2.2 撤销权限

```sql
REVOKE ALL PRIVILEGES ON db_name.* FROM 'username'@'host';
```

---

### 2.3 查看权限

```sql
SHOW GRANTS FOR 'username'@'host';
```

---

### 2.4 刷新权限表（授权后通常自动生效）

```sql
FLUSH PRIVILEGES;
```

---

## 3. 库级元信息查询

### 3.1 查询所有数据库的库名

```sql
SELECT SCHEMA_NAME FROM information_schema.SCHEMATA;
```

---

### 3.2 查询某数据库的所有表

```sql
SELECT TABLE_NAME FROM information_schema.TABLES
WHERE TABLE_SCHEMA = 'db_name';
```

---

### 3.3 查询库大小（所有表文件大小之和）

```sql
SELECT 
  table_schema AS db_name,
  ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS size_mb
FROM information_schema.TABLES
GROUP BY table_schema;
```

---

## 4. 备份 & 恢复（库维度）

### 4.1 备份数据库（使用 `mysqldump`）

```bash
mysqldump -u root -p db_name > db_name.sql
```

---

### 4.2 恢复数据库

```bash
mysql -u root -p db_name < db_name.sql
```

---

## 5. 库级别参数

虽然数据库本身无太多单独参数，但以下全局参数影响库行为：

- `lower_case_table_names`：影响表名是否区分大小写（仅创建时生效）
- `character_set_server`：新建库的默认字符集
- `collation_server`：新建库的默认排序规则
- `sql_mode`：SQL 严格程度（如 `ONLY_FULL_GROUP_BY`）

可以通过以下方式查看：

```sql
SHOW VARIABLES LIKE 'character_set%';
SHOW VARIABLES LIKE 'collation%';
SHOW VARIABLES LIKE 'sql_mode';
```

---

## 6.使用示例

### 一键初始化新库
```sql
CREATE DATABASE IF NOT EXISTS `shop` 
  DEFAULT CHARACTER SET utf8mb4 
  COLLATE utf8mb4_general_ci;

GRANT ALL PRIVILEGES ON shop.* TO 'dev_user'@'%' IDENTIFIED BY '123456';

FLUSH PRIVILEGES;
```

---



