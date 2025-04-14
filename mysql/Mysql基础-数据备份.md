# Mysql基础-数据备份


## 1. 备份类型

| 类型     | 工具            | 特点                                     |
|----------|------------------|------------------------------------------|
| 逻辑备份 | `mysqldump`      | 通用性强，输出 SQL 文件，易于迁移         |
| 物理备份 | `xtrabackup`     | 快速、可热备份，适合大数据量              |
| 冷备     | `tar`, `cp`      | 停库备份整个数据目录                     |
| 快照备份 | LVM、ZFS 等       | 系统层级快照，恢复极快，但依赖平台         |

---

## 2. 使用 `mysqldump` 逻辑备份

### 2.1 备份整个数据库

```bash

mysqldump -u root -p mydb > mydb.sql
```

### 2.2 备份所有数据库

```bash

mysqldump -u root -p --all-databases > all.sql
```

### 2.3 备份部分表

```bash

mysqldump -u root -p mydb table1 table2 > mydb_part.sql
```

### 2.4 带结构不带数据（仅 DDL）

```bash

mysqldump -u root -p -d mydb > schema_only.sql
```

### 2.5 带数据不带结构

```bash

mysqldump -u root -p -t mydb > data_only.sql
```

---

## 3. 常用参数详解（`mysqldump`）

| 参数                       | 说明                                     |
|----------------------------|------------------------------------------|
| `--single-transaction`     | 一致性读（InnoDB 热备）推荐加             |
| `--master-data=2`          | 写入 binlog 位点（用于主从复制）          |
| `--skip-lock-tables`       | 跳过表锁（只读数据库场景）                |
| `--default-character-set`  | 保证编码一致，推荐 `utf8mb4`              |
| `--where`                  | 条件导出，如 `--where="id<1000"`         |
| `--no-data` / `-d`         | 仅导出表结构                              |

---

## 4. 使用 `xtrabackup` 物理备份（适合大库）

> Percona 出品，适用于 InnoDB，支持在线备份（热备），速度远优于 `mysqldump`

[官网](https://www.percona.com/mysql/software/percona-xtrabackup)

### 4.1 安装

```bash

sudo apt install percona-xtrabackup-80
```

### 4.2 全量备份

```bash

xtrabackup --backup --target-dir=/backup/full --user=root --password=xxxx
```

### 4.3 增量备份（基于上次全量）

```bash

xtrabackup --backup --target-dir=/backup/inc1 --incremental-basedir=/backup/full --user=root --password=xxx
```

### 4.4 恢复流程

```bash

# 预处理
xtrabackup --prepare --target-dir=/backup/full

# 恢复
xtrabackup --copy-back --target-dir=/backup/full
chown -R mysql:mysql /var/lib/mysql
```

---

## 5. 冷备（直接复制数据目录）

> 简单粗暴，需停库，适合非高可用环境。

### 步骤：

```bash
# 停服务
systemctl stop mysql

# 复制数据目录
cp -r /var/lib/mysql /backup/mysql_backup

# 重启服务
systemctl start mysql
```

---

## 6. 快照备份（如 LVM）

> 恢复极快，但依赖文件系统

### 步骤概览：

1. 对 MySQL 数据目录建 LVM 快照
2. 复制快照卷内容
3. 卸载快照
4. 恢复时挂载并替换数据目录

---



## 7. 备份恢复注意事项

| 项目         | 建议                                               |
|--------------|----------------------------------------------------|
| 编码一致     | 导出导入均加 `--default-character-set=utf8mb4`    |
| 权限问题     | 恢复后检查 MySQL 数据目录属主是否为 `mysql:mysql` |
| 触发器、事件 | 默认导出，如需排除，加 `--skip-triggers` 等       |
| 外键约束     | 恢复时可暂时关闭 `SET FOREIGN_KEY_CHECKS=0;`      |
| 大表导出慢   | 建议用 `xtrabackup` 或分表分区                    |

---

## 8. 示例

## 8.1 定时自动备份脚本

```bash

#!/bin/bash
DATE=$(date +%F)
mysqldump -u root -p123456 --single-transaction mydb | gzip > /backup/mydb_$DATE.sql.gz
find /backup -mtime +7 -name "*.sql.gz" -delete
```

加到 crontab：

```bash

0 2 * * * /usr/local/bin/mysql_backup.sh
```

---

## 8.2 备份 + 主从日志点（用于主从复制恢复）

```bash

mysqldump -u root -p --single-transaction --master-data=2 mydb > master.sql
```

备份文件中含有：

```sql
-- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000123', MASTER_LOG_POS=45678;
```

---

