# Mysql基础-binlog日志

MySQL 的 **binlog（二进制日志）** 是数据库非常重要的日志系统之一。它记录了所有对数据库执行的 **更改类语句（如 INSERT、UPDATE、DELETE）**，但不记录查询（SELECT）操作。

它是实现 **数据恢复、主从复制、审计等功能的基础**。

---

## 1. binlog 的开启与查看

### 1.1 开启 binlog（MySQL 配置文件）

```ini
[mysqld]
server-id=1
log-bin=mysql-bin
binlog-format=ROW
expire_logs_days=7
```

```sql
-- 在全局级别动态修改 binlog 的格式为 ROW
SET GLOBAL binlog_format = ROW;

-- 在会话级别动态修改 binlog 的格式为 STATEMENT（仅影响当前会话）
SET SESSION binlog_format = STATEMENT;

-- 在全局级别动态修改单个 binlog 文件的大小限制为 100MB
SET GLOBAL max_binlog_size = 100M;

-- 在全局级别动态修改 binlog 的同步方式，设置为每次事务提交都同步到磁盘
SET GLOBAL sync_binlog = 1;
```

### 1.2 查看 binlog 是否开启

```sql
SHOW VARIABLES LIKE 'log_bin';
```

结果为 `ON` 表示开启。

---

## 2. binlog 的三种格式

```sql
SHOW VARIABLES LIKE 'binlog_format';
```

### 2.1 **STATEMENT**（语句模式）
- 记录执行的 SQL 语句。
- 优点：体积小，性能好。
- 缺点：某些非确定性操作可能导致主从数据不一致（如 `NOW()`、`UUID()`）。

### 2.2 **ROW**（行模式）推荐
- 记录每一行被修改前后的内容。
- 优点：数据变更更精确，不依赖语句。
- 缺点：日志量较大。

### 2.3 **MIXED**（混合模式）
- MySQL 会根据语句自动在 Statement 和 Row 之间切换。

---

## 3. binlog 的组成

每个 binlog 文件包含多个 **event**，每个 event 记录了一次操作。

### 常见 Event 类型：
- `Query_event`: 一条 SQL（如 DDL、事务开启/提交）
- `Write_rows_event`: 插入数据（ROW 格式）
- `Update_rows_event`: 更新数据（ROW 格式）
- `Delete_rows_event`: 删除数据（ROW 格式）
- `Xid_event`: 事务提交
- `Format_description_event`: binlog 版本描述
- `Rotate_event`: binlog 文件切换

---

## 4. binlog 的基本操作

### 4.1 查看所有 binlog 文件

```sql
SHOW BINARY LOGS;
```

### 4.2 当前正在写入的 binlog 文件

```sql
SHOW MASTER STATUS;
```

### 4.3 查看 binlog 内容

```bash

mysqlbinlog mysql-bin.000001
```

可以添加 `--start-datetime` 和 `--stop-datetime` 参数。

---

## 5. binlog 与主从复制

- 主库写 binlog
- 从库通过 `IO 线程` 读取 binlog，写入自己的 relay log
- 从库通过 `SQL 线程` 执行 relay log 中的内容，实现数据同步

---

## 6. binlog 与数据恢复

MySQL 使用 **binlog + 备份** 的方式实现数据恢复：

1. 先恢复最近一次完整备份
2. 然后 replay binlog 中的更改操作（可以指定时间或位置点）

```bash

mysqlbinlog --start-datetime="2025-04-13 10:00:00" \
            --stop-datetime="2025-04-13 12:00:00" \
            mysql-bin.000001 | mysql -u root -p
```

---

## 7. binlog 的位置点（position）

每一个 binlog 事件在文件中都有唯一的起始位置（pos），可以用于精确恢复和主从同步。

```sql
SHOW BINLOG EVENTS IN 'mysql-bin.000001';
```

---

## 8. binlog 清理策略

### 自动清理（推荐）：

```ini
expire_logs_days=7   # 7天自动清除
```

或 MySQL 8.0 起使用：

```ini
binlog_expire_logs_seconds=604800  # 单位秒
```

### 手动清理：

```sql
PURGE BINARY LOGS TO 'mysql-bin.000010';
-- 或
PURGE BINARY LOGS BEFORE '2025-04-01 00:00:00';
```

---

## 9. 注意事项与最佳实践

1. 推荐使用 `ROW` 模式，确保主从数据一致。
2. 开启 binlog 后可能稍有性能影响（一般可接受）。
3. 定期清理 binlog 防止磁盘爆满。
4. binlog 与 `undo log`、`redo log` 是互补的，不可混淆。
5. binlog 与 `general log` 和 `slow query log` 作用不同，分别用于不同场景。


