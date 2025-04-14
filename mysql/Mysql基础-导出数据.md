# Mysql基础-导出数据


## 1. 使用 `mysqldump` 工具（最常用方式）

### 1.1 导出整个数据库

```bash

mysqldump -u用户名 -p 数据库名 > dump.sql
```

### 1.2 导出多个数据库

```bash

mysqldump -u用户名 -p --databases db1 db2 > dump.sql
```

### 1.3 导出所有数据库

```bash

mysqldump -u用户名 -p --all-databases > all.sql
```

### 1.4 导出单张表

```bash

mysqldump -u用户名 -p 数据库名 表名 > table.sql
```

### 1.5 导出结构（无数据）

```bash

mysqldump -u用户名 -p -d 数据库名 > schema.sql
```

> `-d` 或 `--no-data` 表示不导出数据，只导出表结构。

---

## 2. 常用参数说明

| 参数 | 说明 |
|------|------|
| `-u` | 用户名 |
| `-p` | 提示输入密码 |
| `-h` | 指定主机 |
| `--single-transaction` | 导出过程中不锁表（InnoDB推荐） |
| `--quick` | 边读边写（适合大表） |
| `--lock-tables` | 锁表导出（默认InnoDB不开启） |
| `--default-character-set=utf8mb4` | 设置字符集，避免乱码 |
| `--no-create-info` | 不导出表结构，只导出数据 |
| `--no-data` | 不导出数据，只导出结构 |
| `--where='条件'` | 导出部分数据 |
| `--column-statistics=0` | MySQL 8+ 防止导出失败（如Navicat导出） |

### 示例：按条件导出部分数据

```bash

mysqldump -u root -p test user --where="age > 25" > partial.sql
```

---

## 3. 导出 CSV 文件（适合表格查看）

### 3.1 使用 `SELECT ... INTO OUTFILE`

```sql
SELECT * FROM user
INTO OUTFILE '/var/lib/mysql-files/user.csv'
FIELDS TERMINATED BY ','
OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

> 注意权限：
> - `secure_file_priv` 参数必须允许写入（使用 `SHOW VARIABLES LIKE 'secure_file_priv';` 查看）
> - 文件路径必须在允许范围内，且服务器具备写权限

### 3.2 可选字段控制示例

```sql
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
ESCAPED BY '\\' 
LINES TERMINATED BY '\r\n';
```

---

## 4. 导出视图、触发器、函数、事件

```bash

mysqldump -u root -p \
--routines --events --triggers \
数据库名 > full_dump.sql
```

---

## 5. 压缩导出文件（节省空间）

```bash

mysqldump -u root -p 数据库名 | gzip > dump.sql.gz
```

恢复时：

```bash

gunzip < dump.sql.gz | mysql -u root -p
```

---


## 6. 权限要求

使用 `mysqldump` 要求用户具有以下权限：

- 对导出表：`SELECT`
- 若导出触发器、函数等：需要 `TRIGGER`、`SHOW VIEW`、`EVENT`、`EXECUTE` 等权限
- 导出所有库：需要 `SELECT`、`LOCK TABLES`、`SHOW VIEW`、`RELOAD`

---

## 7. 导出注意事项总结

| 注意点 | 建议 |
|--------|------|
| 表结构乱码 | 加上 `--default-character-set=utf8mb4` |
| 大表导出慢 | 加上 `--quick` `--single-transaction` |
| 避免锁表 | 推荐 `--single-transaction`（仅支持 InnoDB） |
| 时间戳精度丢失 | 确认 `sql_mode` 未影响数据类型 |
| 视图无法导出 | 加上 `--routines --events --triggers` |
| CSV 导出失败 | 检查 `secure_file_priv` 设置是否为空或指定目录 |

---

## 8. 示例

```bash

mysqldump -u root -p \
--single-transaction --quick --default-character-set=utf8mb4 \
--routines --events --triggers \
mydb > mydb_full.sql
```

---
