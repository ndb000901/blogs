# Mysql基础-undo日志


在 MySQL 8（特别是 InnoDB 存储引擎）中，**Undo 日志（回滚日志）**是事务回滚、MVCC 实现的核心组件。它记录的是数据**修改前的值（旧值）**，以便在必要时回滚操作或实现一致性读。

---

## 1. Undo 日志

Undo 日志是 InnoDB 引擎维护的**逻辑日志**，记录了数据**修改之前的版本**，用于：

- 事务回滚（Undo）
- 实现一致性读（MVCC）

---

## 2. Undo 日志的作用

| 场景 | 作用 |
|------|------|
| 回滚事务 | 可以使用 Undo 恢复原来的值 |
| MVCC | 多版本并发控制依赖 Undo 保留的旧版本 |
| 崩溃恢复 | Rollback 阶段需要用到 Undo |
| GAP 锁、Next-Key Locks | 部分操作也可能依赖 Undo |

---

## 3. Undo 日志记录了什么？

当执行如下 SQL 时：

```sql
UPDATE users SET name = 'Tom' WHERE id = 1;
```

- 修改 `name` 字段的同时，会生成 Undo 日志，记录修改前的数据，比如：`name='Jerry'`

- 如果事务被回滚，InnoDB 就会使用 Undo 恢复 `name='Jerry'`

---

## 4. Undo 日志的分类

根据操作类型，Undo 分为两类：

| 类型 | 说明 | 举例 |
|------|------|------|
| **Insert Undo** | 用于回滚 `INSERT` 操作 | `INSERT INTO ...` |
| **Update Undo** | 用于回滚 `UPDATE`、`DELETE` | `UPDATE ...`、`DELETE ...` |

其中：
- Insert Undo 不参与 MVCC（因为新插入的数据对其他事务不可见）
- Update Undo 参与 MVCC

---

## 5. Undo 与 MVCC（多版本并发控制）

InnoDB 的**快照读（Snapshot Read）** 依赖 Undo 来访问“旧版本”数据。

- 每条记录都有**隐藏字段**：
    - `trx_id`：最后修改这条记录的事务 ID
    - `roll_pointer`：指向 Undo 日志的指针（即历史版本）

- 当执行 `SELECT`（非加锁查询）时，InnoDB：
    1. 根据当前事务的 **Read View**
    2. 判断记录是否可见
    3. 如果不可见，则根据 `roll_pointer` 向 Undo 链回溯历史版本
    4. 找到一个可见版本后返回

---

## 6. Undo 日志的存储位置

- Undo 日志存储在**回滚段（Rollback Segment）** 中
- 每个回滚段中有多个 **Undo Log Segment**
- Undo 实体数据存在**共享表空间（.ibd）或 undo 表空间（.ibu）**

相关配置项：

```ini
# 是否使用独立 undo 表空间
innodb_undo_tablespaces = 2      # MySQL 8 默认使用独立表空间

# 每个 undo 表空间中回滚段个数
innodb_rollback_segments = 128
```

---

## 7. Undo 的回收与清理

为了防止 Undo 日志无限增长，MySQL 需要**回收**这些旧版本：

- 有一个后台线程 `purge` 负责清除 **已经不再需要的 Undo**
- 前提是：**没有任何事务需要依赖这些 Undo（如快照读）**

你可以通过以下命令查看 purge 状态：

```sql
SHOW ENGINE INNODB STATUS\G
```

关注字段：
- `History list length`（历史链长度，越小越好）

---

## 8. Undo 的生命周期

一条 Undo 日志的大致生命周期如下：

```
事务开始
  ↓
修改数据 → 记录 Undo
  ↓
事务提交
  ↓
Undo 仍保留，供其他事务快照读取（MVCC）
  ↓
所有事务都不再需要 → 被 purge 清理
```

---

## 9. Undo 的存储结构（简化图）

```
┌──────────────┐
│ 数据页 (记录)│
└──┬────┬──────┘
   │    │
   │    └──► trx_id（最后一次修改事务）
   │
   └────► roll_pointer ─────┐
                            ▼
                       Undo Log Segment
                            ↓
                     Undo Log Record (旧版本值)
                            ↓
                        Undo 链（多版本）
```

---

## 10. Undo 相关的性能建议

- 保持事务短小，避免长事务阻碍 Undo 清理
- 控制 `innodb_undo_tablespaces` 数量（太少可能阻塞 purge，太多占空间）
- 监控 `History list length`，避免长时间积压

---

## 11. Undo 与 Redo 的关系

| 类别 | Undo | Redo |
|------|------|------|
| 类型 | 逻辑日志（旧值） | 物理日志（新值） |
| 用途 | 回滚、MVCC | 崩溃恢复 |
| 写入时机 | 事务执行时 | 事务执行时 |
| 回滚时 | 依赖 Undo | 无关 |
| 提交时 | 留存供 MVCC | 必须 fsync |

---
