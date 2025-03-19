# Mysql基础-库管理

## 1. **创建数据库**（`CREATE DATABASE`）
创建数据库时，可以指定字符集、排序规则等。

### **基本语法**
```sql
create database 数据库名;
```
### **示例**
```sql
create database mydb;
```
**指定字符集和排序规则**
```sql
create database mydb character set utf8mb4 collate utf8mb4_unicode_ci;
```
> **注意**：  
> - `utf8mb4` 是 MySQL 8 推荐的字符集，支持完整的 UTF-8 编码（包括 emoji）。
> - `COLLATE utf8mb4_unicode_ci` 适用于不区分大小写的排序。

---

## 2. **查看数据库**（`show databases`）
### **基本语法**
```sql
show databases;
```
### **示例**
```sql
show databases;
```
**查看某个数据库的字符集**
```sql
select schema_name, default_character_set_name, default_collation_name
from information_schema.schemata
where schema_name = 'mydb';
```

---

## 3. **使用数据库**（`use`）
如果要执行数据库中的操作，需要先切换到目标数据库。

### **基本语法**
```sql
use 数据库名;
```
### **示例**
```sql
use mydb;
```

---

## 4. **修改数据库**（`alter database`）
MySQL 8 允许修改数据库的字符集和排序规则。

### **基本语法**
```sql
alter database 数据库名 character set 字符集 collate 排序规则;
```
### **示例**
```sql
alter database mydb character set utf8mb4 collate utf8mb4_general_ci;
```
> **注意**：  
> - 这不会影响已存在的表的字符集，仅影响新建的表。

---

## 5. **删除数据库**（`drop database`）
删除数据库会永久清除数据库及其中的所有表和数据，操作前需谨慎。

### **基本语法**
```sql
drop database 数据库名;
```
### **示例**
```sql
drop database mydb;
```
> **注意**：
> - `drop database` 操作不可逆，删除后无法恢复。

---

## 6. **数据库备份与恢复**
MySQL 8 提供了 `mysqldump` 和 `mysql` 命令行工具进行数据库的备份与恢复。

### **备份数据库**
```bash
mysqldump -u root -p mydb > mydb_backup.sql
```

### **恢复数据库**
```bash
mysql -u root -p mydb < mydb_backup.sql
```

### **只导出表结构**
```bash
mysqldump -u root -p --no-data mydb > mydb_schema.sql
```

### **只导出数据**
```bash
mysqldump -u root -p --no-create-info mydb > mydb_data.sql
```

---

## 7. **查看数据库的表**（`show tables`）
如果想查看某个数据库中所有的表，可以使用 `show tables`。

### **基本语法**
```sql
show tables;
```
### **示例**
```sql
use mydb;
show tables;
```

---

## 8. **查看数据库大小**
MySQL 8 允许通过 `information_schema` 查询数据库的大小。

### **示例**
```sql
select table_schema as `database`,
       round(sum(data_length + index_length) / 1024 / 1024, 2) as `size (mb)`
from information_schema.tables
where table_schema = 'mydb'
group by table_schema;
```

---



