# Mysql基础-表管理

---

## 1. 创建表

### 1.1 基础语法

```sql
CREATE TABLE table_name (
  column1 datatype [constraints],
  column2 datatype [constraints],
  ...
);
```

### 1.2 示例：带主键、默认值、自动递增等

```sql
CREATE TABLE user (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE utf8mb4_general_ci;
```

- `ENGINE=InnoDB`：使用事务、行级锁等
- `CHARSET=utf8mb4`：支持 emoji、全 Unicode
- 排序规则推荐 `utf8mb4_general_ci`（不区分大小写）或 `utf8mb4_bin`（区分大小写）。

---

## 2. 查看表结构

```sql
DESC table_name;
```

更详细结构：

```sql
SHOW CREATE TABLE table_name\G
```

从 `information_schema` 中查结构：

```sql
SELECT COLUMN_NAME, DATA_TYPE, COLUMN_TYPE, IS_NULLABLE, COLUMN_KEY
FROM information_schema.COLUMNS
WHERE TABLE_NAME = 'table_name'
  AND TABLE_SCHEMA = 'db_name';
```

---

## 3. 修改表结构（ALTER）

### 3.1 添加列

```sql
ALTER TABLE user ADD COLUMN age INT AFTER name;
```

### 3.2 删除列

```sql
ALTER TABLE user DROP COLUMN age;
```

### 3.3 修改列类型

```sql
ALTER TABLE user MODIFY COLUMN name VARCHAR(200);
```

或同时修改列名：

```sql
ALTER TABLE user CHANGE COLUMN name username VARCHAR(200);
```

### 3.4 添加主键/唯一键/索引

```sql
ALTER TABLE user ADD PRIMARY KEY (id);
ALTER TABLE user ADD UNIQUE (email);
ALTER TABLE user ADD INDEX idx_name (name);
```

### 3.5 删除索引

```sql
ALTER TABLE user DROP INDEX idx_name;
```

---

## 4. 删除表（危险）

```sql
DROP TABLE table_name;
```

建议加判断：

```sql
DROP TABLE IF EXISTS table_name;
```

删除多个表：

```sql
DROP TABLE table1, table2;
```

---

## 5. 重命名表

```sql
RENAME TABLE old_table_name TO new_table_name;
```

---

## 6. 复制表结构 & 数据

### 6.1 只复制结构（不含数据）

```sql
CREATE TABLE new_table LIKE old_table;
```

### 6.2 复制结构 + 数据

```sql
CREATE TABLE new_table AS SELECT * FROM old_table;
```

使用 `AS SELECT` 创建的表不会复制索引、主键等，仅复制列结构。

---

## 7. 清空表数据（TRUNCATE）

```sql
TRUNCATE TABLE table_name;
```

快速清空数据（效率高于 DELETE）  
自增主键会从头重新计数  
不可回滚（在事务中会自动提交）

---

## 8. 表优化相关

### 8.1 查看表状态

```sql
SHOW TABLE STATUS LIKE 'table_name'\G
```

可以看到行数、大小、更新时间等。

### 8.2 优化表（回收空间）

```sql
OPTIMIZE TABLE table_name;
```

适用于频繁 `DELETE` 或 `UPDATE` 后的表，能整理碎片、重建表文件。

---

## 9. 表数据与统计信息分析

```sql
ANALYZE TABLE table_name;
```

用于更新存储引擎维护的统计信息，帮助优化器选择更优的执行计划。

---

## 10. 表锁信息查看（InnoDB）

```sql
SELECT * FROM information_schema.INNODB_LOCKS;
```

查看锁等待关系：

```sql
SELECT * FROM information_schema.INNODB_LOCK_WAITS;
```

---

## 11. 表关联信息查询

### 11.1 表所属数据库、引擎等信息

```sql
SELECT * FROM information_schema.TABLES
WHERE TABLE_NAME = 'user';
```

### 11.2 查看某个表的所有索引

```sql
SHOW INDEX FROM table_name;
```

---

## 12. 表分区

```sql
CREATE TABLE sales (
  id INT,
  created_at DATE
)
PARTITION BY RANGE (YEAR(created_at)) (
  PARTITION p2019 VALUES LESS THAN (2020),
  PARTITION p2020 VALUES LESS THAN (2021),
  PARTITION pmax VALUES LESS THAN MAXVALUE
);
```

---
