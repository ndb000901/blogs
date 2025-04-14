# Mysql函数-ReplicationFunctions

MySQL 8 中的 **Replication Functions（复制函数）** 是用于监控和控制主从复制（包括传统复制和基于 GTID 的复制）状态的内置函数。它们帮助开发者和 DBA 获取当前复制状态、延迟情况，或强制设置复制状态。

---

## 1. 复制类型概览

MySQL 支持多种复制方式：

1. **基于二进制日志的传统复制（Statement-Based / Row-Based / Mixed）**
2. **基于 GTID（全局事务 ID）复制**
3. **异步复制 / 半同步复制**
4. **组复制（Group Replication）**

---

## 2. Replication Functions 列表（MySQL 8）

| 函数名 | 说明 |
|--------|------|
| `MASTER_POS_WAIT(log_file, log_pos[, timeout])` | 等待主库执行到指定的 binlog 位置 |
| `MASTER_GTID_WAIT(gtid_set[, timeout])` | 等待主库执行到指定 GTID |
| `WAIT_UNTIL_SQL_THREAD_AFTER_GTIDS(gtid_set[, timeout])` | 等待从库 SQL 线程追上指定 GTID |
| `GTID_SUBSET(set1, set2)` | 判断 `set1` 是否为 `set2` 的子集 |
| `GTID_SUBTRACT(set1, set2)` | 返回 `set1` 中不在 `set2` 中的 GTID |
| `WAIT_FOR_EXECUTED_GTID_SET(gtid_set[, timeout])` | 等待当前会话执行完指定 GTID 集合（Group Replication 中常用） |

---

## 3. 函数详解

### 3.1 `MASTER_POS_WAIT(log_file, log_pos[, timeout])`

- **作用**：阻塞当前会话，直到主库的 binlog 位置执行到指定位置。
- **用途**：适用于传统复制，不支持 GTID。
- **参数**：
    - `log_file`: binlog 文件名（如 `'mysql-bin.000001'`）
    - `log_pos`: binlog 位置
    - `timeout`: 可选，超时时间（秒）

```sql
SELECT MASTER_POS_WAIT('mysql-bin.000001', 120);
```

返回值：

- 正数：等待成功，返回等待时间（秒）
- `NULL`：发生错误
- `-1`：超时

---

### 3.2 `MASTER_GTID_WAIT(gtid_set[, timeout])`

- **作用**：等待主库执行到指定 GTID。
- **用途**：用于 GTID 模式下复制。
- **示例**：

```sql
SELECT MASTER_GTID_WAIT('3E11FA47-71CA-11E1-9E33-C80AA9429562:23');
```

返回值和 `MASTER_POS_WAIT` 类似。

---

### 3.3 `WAIT_UNTIL_SQL_THREAD_AFTER_GTIDS(gtid_set[, timeout])`

- **作用**：等待从库 SQL 线程执行到指定 GTID。
- **用途**：用于检测从库追上主库的进度。

```sql
SELECT WAIT_UNTIL_SQL_THREAD_AFTER_GTIDS('uuid:1-100');
```

---

### 3.4 `GTID_SUBSET(set1, set2)`

- **作用**：判断 set1 是否包含于 set2。
- **返回值**：
    - `1`：是子集
    - `0`：不是
    - `NULL`：无效 GTID

```sql
SELECT GTID_SUBSET('uuid:1-10', 'uuid:1-20');  -- 返回 1
```

---

### 3.5 `GTID_SUBTRACT(set1, set2)`

- **作用**：返回 `set1` 中不在 `set2` 中的 GTID。
- **示例**：

```sql
SELECT GTID_SUBTRACT('uuid:1-10', 'uuid:1-5');
-- 返回: uuid:6-10
```

---

### 3.6 `WAIT_FOR_EXECUTED_GTID_SET(gtid_set[, timeout])`

- **作用**：阻塞当前会话，直到服务器执行了所有 GTID 集合中包含的事务。
- **常用于**：组复制或多源复制场景。

```sql
SELECT WAIT_FOR_EXECUTED_GTID_SET('uuid:1-100');
```

---

## 4. 应用示例

### 4.1 等待主从一致

```sql
-- 在从库上执行，等待追上主库某 GTID
SELECT WAIT_UNTIL_SQL_THREAD_AFTER_GTIDS('uuid:1-50', 10);
```

### 4.2 通过 GTID 判断主从是否一致

```sql
-- 检查从库是否落后主库
SELECT GTID_SUBSET(
  (SELECT @@GLOBAL.GTID_EXECUTED),
  'uuid:1-100'
);
```
