# Mysql基础-主从复制

MySQL 主从复制（Replication）是实现高可用、读写分离、数据备份与容灾的重要机制之一。

---

## 1. 主从复制

**主库（Master）**：处理写操作，并记录 binlog。  
**从库（Slave / Replica）**：从主库读取 binlog 并重放，保持数据一致。

```text
Client -> Master -> binlog -> Slave -> Relay log -> SQL thread
```

---

## 2. 复制的基本流程

1. **主库写入 binlog**
2. **从库的 I/O 线程连接主库，读取 binlog，写入 relay log（中继日志）**
3. **从库的 SQL 线程从 relay log 中读取事件，执行 SQL 语句**

---

## 3. 复制模式

| 模式          | 描述 |
|---------------|------|
| **异步复制**     | 默认模式，主库提交后即返回，从库可能延迟 |
| **半同步复制**   | 主库至少等待一个从库确认 binlog 写入 |
| **组复制（Group Replication）** | 多主高可用，基于 Paxos 的分布式复制 |

---

## 4. 设置主从复制（基础版）

### 4.1 主库配置

```ini
# my.cnf
server-id = 1
log-bin = mysql-bin
binlog_format = row
```

执行授权：

```sql
CREATE USER 'repl'@'%' IDENTIFIED WITH mysql_native_password BY 'repl_password';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
FLUSH PRIVILEGES;
```

查看位点：

```sql
SHOW MASTER STATUS;
```

### 4.2 从库配置

```ini
server-id = 2
relay-log = relay-log
log-bin = mysql-bin # 如需从库再复制
read-only = 1       # 可防止误写
```

连接主库：

```sql
CHANGE MASTER TO
  MASTER_HOST='主库IP',
  MASTER_USER='repl',
  MASTER_PASSWORD='repl_password',
  MASTER_LOG_FILE='mysql-bin.000001',
  MASTER_LOG_POS=12345;

START SLAVE;
```

检查状态：

```sql
SHOW SLAVE STATUS\G
```

关注字段：
- `Slave_IO_Running: Yes`
- `Slave_SQL_Running: Yes`

---

## 5. GTID 模式复制（推荐）

GTID（全局事务 ID）是 MySQL 5.6+ 引入的新机制，简化复制管理。

### 主库配置

```ini
server-id = 1
log-bin = mysql-bin
gtid_mode = ON
enforce-gtid-consistency = ON
```

从库配置类似。

### 启动 GTID 复制

```sql
CHANGE MASTER TO MASTER_AUTO_POSITION = 1;
START SLAVE;
```

**优点**：
- 更容易切换主从
- 更容易做故障恢复
- 避免 binlog 位点手动设置

---

## 6. 常见主从复制场景设计

### 6.1 读写分离

- 写：主库
- 读：多个从库

通过中间件（如 MyCat、ShardingSphere）或应用自行路由来实现。

### 6.2 主从切换

- 可以使用 `MHA`、`Orchestrator` 做主从自动切换
- 配合 `GTID` 更安全

### 6.3 延迟复制

```sql
CHANGE MASTER TO MASTER_DELAY = 3600;
```

从库延迟 1 小时执行：防止误删除时可以找回。

---

## 7. 复制故障处理

### 错误 1：主从数据不一致

- 可用 `pt-table-checksum`、`pt-table-sync` 工具处理
- 尽量避免非事务表 + 非 row 模式

### 错误 2：主从断开，无法连接

检查：
- 用户权限
- 网络连通性
- 主库 binlog 是否被清理

可用 `RESET SLAVE;` 清除错误记录

---

## 8. binlog_format 建议

- `row` 模式：记录每行变更，推荐（最精确）
- `statement` 模式：记录 SQL，体积小但易出错
- `mixed` 模式：自动选择

---

## 9. 主从复制常用命令

```sql
-- 开启复制
START SLAVE;

-- 停止复制
STOP SLAVE;

-- 查看复制状态
SHOW SLAVE STATUS\G;

-- 使用 GTID 自动连接主库
CHANGE MASTER TO MASTER_AUTO_POSITION = 1;

-- 跳过错误
SET GLOBAL SQL_SLAVE_SKIP_COUNTER = 1;

-- 忘记主库（慎用）
RESET SLAVE ALL;
```

---

