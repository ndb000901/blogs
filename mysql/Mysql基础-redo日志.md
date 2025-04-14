# Mysql基础-redo 日志

在 MySQL（特别是 InnoDB 存储引擎）中，**Redo 日志（重做日志）** 是事务持久化的核心机制，确保即使数据库宕机，提交的数据也不会丢失。

---

## 1. Redo 日志

Redo 日志是 InnoDB 引擎提供的 **物理日志**，记录的是数据页的物理修改操作，主要用于数据库 **崩溃恢复**。

> 简单说：**先写日志，再改数据**，防止数据丢失。

---

## 2. Redo 日志的作用

- **保证事务持久性（Durability）**：事务提交前会先写 Redo 日志，即使宕机，也能在重启时“重放日志”恢复数据。
- **支持崩溃恢复（Crash Recovery）**：系统宕机后通过 Redo 日志恢复“已提交但未写盘”的数据。

---

## 3. Redo 日志与其他日志的区别

| 日志类型 | 作用 | 写入时间 | 用途 |
|----------|------|----------|------|
| **Redo Log** | 记录物理页更改 | 事务执行时 | 保证崩溃后数据恢复 |
| **Undo Log** | 记录逻辑操作（如旧值） | 事务执行时 | 回滚事务，支持 MVCC |
| **Binlog** | 记录所有更改（逻辑日志） | 事务提交时 | 复制、备份、恢复 |

---

## 4. Redo 日志的写入流程（WAL）

MySQL 采用 **WAL（Write-Ahead Logging）机制**：

1. 修改 Buffer Pool 中的内存页。
2. 生成 Redo 日志并写入 `redo log buffer`。
3. 写入 OS 缓存（不一定马上写磁盘）。
4. 当事务提交时：
    - 强制刷新 Redo log buffer 到磁盘（`ib_logfileX`），即 `fsync()`。
    - 更新 binlog（在支持 binlog 的场景下）。

---

## 5. Redo 日志结构详解

Redo 日志由两部分组成：

| 组件            | 说明 |
|-----------------|------|
| **Redo log buffer** | 内存中的缓冲区，用于临时保存 redo 日志条目 |
| **Redo log file**   | 磁盘上的物理文件，默认两个轮转文件：`ib_logfile0`、`ib_logfile1` |

### Redo 日志文件参数配置：

```ini
# 每个 redo log 文件的大小（单位：MB）
innodb_log_file_size = 512M

# Redo 日志文件数量（推荐设置为 2）
innodb_log_files_in_group = 2
```

总大小 = `innodb_log_file_size` × `innodb_log_files_in_group`

---

## 6. Redo 日志的写入策略

- `innodb_flush_log_at_trx_commit` 控制 **事务提交时 Redo 刷新策略**：

| 参数值 | 含义 | 性能 | 安全性 |
|--------|------|------|--------|
| 0      | 每秒写一次（由后台线程） | 高 | 低，宕机可能丢数据 |
| 1（默认） | 每次提交都 `fsync` | 低 | 高，最安全 |
| 2      | 提交时写 OS 缓存，每秒刷盘 | 中 | 中，可能丢 OS 缓存中的日志 |

- **一般业务高并发下可以考虑设置为 2 + 使用 UPS 电源**

---

## 7. Redo 日志工作原理图解

```
用户提交事务
   ↓
修改 Buffer Pool 中的页
   ↓
写 Redo 日志（到 redo log buffer）
   ↓
根据 flush 策略刷入 redo log file
   ↓
事务提交完成
```

---

## 8. 崩溃恢复流程（Crash Recovery）

1. **重做阶段（Roll Forward）**
    - 读取 Redo 日志，找到已提交但未刷盘的操作，重放修改。

2. **回滚阶段（Roll Back）**
    - 回滚未提交事务（依赖 Undo log）

---

## 9. Redo 与 Doublewrite 的关系

InnoDB 还有一个 **Doublewrite Buffer 机制** 来解决数据页写入时可能发生“页损坏”的问题：

- 写入页前，会先将数据页写入 doublewrite buffer（共享表空间的一块区域）
- 再将其写入最终位置
- 如果宕机中断写入，可以通过 doublewrite + redo 进行恢复

---
**Doublewrite（双写）**

Doublewrite 是一种在将数据页写入磁盘上的实际位置之前，先将其写入到一个称为 Doublewrite Buffer 的中间存储区域的机制。它主要用于解决 **“torn page”（页撕裂）** 的问题。

* **页撕裂问题：** 当数据库在写入一个 16KB 的数据页到磁盘的过程中发生崩溃时，可能只有部分数据被写入，导致数据页内容不完整和损坏。
* **Doublewrite 的工作方式：**
    1.  当 InnoDB 需要将 Buffer Pool（缓冲池）中修改过的数据页刷新到磁盘时，它首先将这个完整的数据页写入到 **Doublewrite Buffer** 中。Doublewrite Buffer 位于系统表空间中，是一个连续的存储区域。
    2.  然后，InnoDB 会将 Doublewrite Buffer 中的内容刷新到磁盘。由于是顺序写入，这个过程通常很快。
    3.  只有当数据页成功写入到 Doublewrite Buffer 并刷新到磁盘后，InnoDB 才会将该数据页写入到其在数据文件中的实际目标位置。
* **崩溃恢复与 Doublewrite：**
    1.  在数据库崩溃恢复时，InnoDB 首先会检查数据页的校验和。如果发现某个数据页可能发生了页撕裂（校验和不匹配），InnoDB 会去 Doublewrite Buffer 中查找该页的副本。
    2.  如果能在 Doublewrite Buffer 中找到完整的、正确的页副本，InnoDB 会将该副本恢复到数据文件的相应位置。
    3.  之后，InnoDB 才会应用 Redo Log 中记录的对该页的修改，确保数据的一致性和完整性。





## 10. 查看与分析 Redo 日志

虽然 Redo 日志本质是二进制文件，无法直接查看，但可以通过以下方式监控：

```sql
SHOW ENGINE INNODB STATUS\G
```

查看其中的：

- **Log sequence number (LSN)**：当前 redo 的偏移
- **Flushed up to**：已刷入磁盘的 redo LSN
- **Last checkpoint at**：最近一次 checkpoint 的位置

---

## 11. 调优建议

- Redo 日志文件设置适当大小（大事务需更大）；
- 保持 `innodb_flush_log_at_trx_commit=2` 可提高性能；
- 使用 SSD 提高 redo 的写入吞吐；
- 合理配置 `innodb_log_buffer_size`，防止频繁 flush；
- 注意 checkpoint 频率，避免日志写满。

