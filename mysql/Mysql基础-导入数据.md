# Mysql基础-导入数据

## 1. 导入 `.sql` 文件

### 方式 1：命令行导入

```bash

mysql -u 用户名 -p 数据库名 < dump.sql
```

**示例：**

```bash

mysql -u root -p test < dump.sql
```

> 注意：`dump.sql` 文件内必须有 `CREATE DATABASE` 或使用前先手动建库并 `USE`。

---

## 2. 导入 `.sql.gz` 压缩文件

```bash

gunzip < dump.sql.gz | mysql -u root -p 数据库名
```

---

## 3. 使用 MySQL 客户端交互式导入

```bash

mysql -u root -p
```

进入后：

```sql
USE mydb;
SOURCE /path/to/dump.sql;
```

---

## 4. 导入 CSV 文件

### 4.1 使用 `LOAD DATA INFILE`

```sql
LOAD DATA INFILE '/var/lib/mysql-files/users.csv'
INTO TABLE users
FIELDS TERMINATED BY ',' 
OPTIONALLY ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

- `IGNORE 1 ROWS`：跳过表头
- `secure_file_priv`：限制导入路径（`SHOW VARIABLES LIKE 'secure_file_priv';`）

> 需要 `FILE` 权限

---

### 4.2 若 `secure_file_priv` 不为空，只能导入该目录下的文件：

```bash

SHOW VARIABLES LIKE 'secure_file_priv';
```

### 如果限制为 `/var/lib/mysql-files/`，需把文件拷贝过去：

```bash

sudo cp users.csv /var/lib/mysql-files/
```

---

## 5. 导入时常用选项解析

| 场景 | 推荐参数/操作 |
|------|----------------|
| 大量数据导入 | 使用 `LOAD DATA INFILE` |
| 表已存在 | `INSERT IGNORE` 或 `REPLACE` |
| 插入失败排查 | `SHOW WARNINGS` |
| 自动提交慢 | `SET autocommit=0;`、`COMMIT;` 配合使用 |

---


## 6. 导入 JSON 示例（先转为 INSERT）

MySQL 8 支持 JSON 类型，但不直接导入 `.json` 文件。可借助脚本工具将 JSON 转为 SQL：

```sql
INSERT INTO user(data) VALUES ('{ "name": "Tom", "age": 25 }');
```

---

## 7. 权限要求

| 操作 | 所需权限 |
|------|----------|
| 导入 SQL | `INSERT`, `UPDATE`, `DELETE`, `CREATE`, `DROP` |
| `LOAD DATA INFILE` | 需要 `FILE` 权限 |

---

## 8. 导入常见错误排查

| 错误信息 | 原因与解决 |
|----------|------------|
| `ERROR 1044` | 用户没有访问该库的权限 |
| `ERROR 1049 (Unknown database)` | 目标数据库未创建，请先 `CREATE DATABASE` |
| `ERROR 1290 - secure_file_priv` | 受限路径，请将文件放到允许目录下 |
| `Access denied for user ...` | 用户权限不足，检查 `FILE` 权限 |
| `Malformed packet` / 乱码 | SQL 文件编码与 MySQL 不一致，建议 UTF-8 |
| 导入乱码 | 加参数：`--default-character-set=utf8mb4` |

---

## 9. 示例

### 导出脚本：

```bash

#!/bin/bash
mysqldump -u root -p密码 --single-transaction --default-character-set=utf8mb4 mydb | gzip > mydb.sql.gz
```

### 导入脚本：

```bash

#!/bin/bash
gunzip < mydb.sql.gz | mysql -u root -p密码 mydb
```

---


