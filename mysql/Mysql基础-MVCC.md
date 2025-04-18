# Mysql基础-MVCC


## 1. MVCC

**多版本并发控制(Multi-Version Concurrency Control)**

- 每条记录在更新时，不是覆盖旧值，而是**保留旧版本**（Undo Log 中）
- 不同事务在读取时，看到的是**自己视角下的版本**
- 避免了读写冲突，无需加锁，提升性能

MVCC 的目标：

| 目标              | 实现方式                  |
|-------------------|---------------------------|
| 提高并发性         | 避免加锁                   |
| 保证一致性视图     | 基于版本号进行“时间穿越”     |
| 支持一致性读（快照）| 使用 Undo Log 构造历史快照  |

---

## 2. MVCC 依赖哪些底层机制

MVCC 依赖以下三项核心：

| 名称             | 说明                                         |
|------------------|----------------------------------------------|
| `DB_TRX_ID`      | 每条记录的最近一次修改者（事务ID）            |
| `DB_ROLL_PTR`    | 指向 Undo Log，用于构造旧版本                 |
| Undo Log         | 记录旧版本数据，支持回滚和一致性读           |
| Read View        | 当前事务创建时的视图，决定能“看到谁”的版本   |

---

## 3. Read View

**Read View = 一致性快照**

当你执行：
```sql
SELECT * FROM user WHERE id = 1;
```

如果这个 `SELECT` 是在事务里执行的，MySQL 就会创建一个 Read View，它会记录：

| 项     | 含义 |
|--------|------|
| `m_ids` | 当前活跃事务ID列表 |
| `min_trx_id` | 活跃事务中最小的 ID |
| `trx_id` | 当前事务的 ID |

Read View 用来判断：**当前事务能否看到某条记录的版本。**


### 不同隔离级别下 Read View 的创建时机：

#### **READ COMMITTED（读已提交）隔离级别：**

- 在 `READ COMMITTED` 隔离级别下，**每次执行一致性读操作（例如普通的 `SELECT` 语句）时，都会创建一个新的 Read View。**
- 这意味着在同一个事务中，如果执行多次 `SELECT` 语句，可能会因为其他并发事务的提交而看到不同的数据版本。

#### **REPEATABLE READ（可重复读）隔离级别（InnoDB 的默认级别）：**

- 在 `REPEATABLE READ` 隔离级别下，**事务在第一次执行一致性读操作时，会创建一个 Read View，并且这个 Read View 会被该事务的所有后续一致性读操作重用。**
- 这保证了在同一个事务中，多次读取相同的数据会看到一致的结果，即使其他并发事务已经提交了修改。

#### **READ UNCOMMITTED（读未提交）隔离级别：**
- 在这个隔离级别下，事务可以读取到其他事务尚未提交的数据（脏读）。因此，**通常不需要创建 Read View 来进行版本控制。** 事务会直接读取最新的数据版本，而无需考虑其他事务的状态。

#### **SERIALIZABLE（串行化）隔离级别：**
- `SERIALIZABLE` 提供了最高的隔离级别，它通过对读取的数据也加上共享锁来实现。虽然 InnoDB 内部仍然可能使用 MVCC 的一些机制，但从概念上讲，**这个级别更多地依赖于锁机制来保证事务的串行执行，而不是像 `READ COMMITTED` 和 `REPEATABLE READ` 那样主要依赖 Read View 进行版本控制。**


---

## 4. MVCC 的可见性判断逻辑 (REPEATABLE READ)

判断一个记录的版本是否对当前事务可见：

1. 如果记录的 `DB_TRX_ID < ReadView.min_trx_id`：可见（已经提交）
2. 如果记录的 `DB_TRX_ID = 当前事务ID`：可见（自己写的）
3. 如果记录的 `DB_TRX_ID ∈ 活跃事务ID列表`：不可见
4. 如果记录的 `DB_TRX_ID > 当前事务ID`：不可见（未来数据）

可见性判断依赖于记录中的 `DB_TRX_ID` 和当前事务的 `ReadView`

---

## 5. 一致性快照读 VS 当前读

### 5.1 **一致性快照读 (Consistent Snapshot Read)**

**定义：** 一致性快照读是指 InnoDB 通过 MVCC（多版本并发控制）机制读取数据的方式。普通的 `SELECT` 语句在 `READ COMMITTED` 和 `REPEATABLE READ` 隔离级别下默认执行的是一致性快照读。

**特点：**
- **读取历史版本：** 一致性快照读会读取数据库在某个时间点的快照数据，而不是最新的提交版本。
- **非阻塞：** 进行快照读的事务不会对所读取的行加锁，因此不会阻塞其他事务对同一行数据的修改（写操作）。
- **版本可见性：** 事务能够看到哪个版本的数据取决于其隔离级别：
  - **READ COMMITTED：** 事务每次执行快照读时，都读取最新的已提交版本。
  - **REPEATABLE READ：** 事务在第一次执行快照读时会创建一个 Read View，后续的快照读都基于这个 Read View，因此在整个事务期间看到的数据是一致的（首次读取时的快照）。
* **适用场景：** 大部分普通的查询场景，用于获取数据而不需要立即看到其他并发事务的最新修改。

### 5.2 **当前读 (Current Read)**

**定义：** 当前读是指读取数据库最新的版本数据，并且在读取时会获取必要的锁，以保证其他事务不会在当前事务操作期间修改这些数据。

**特点：**
- **读取最新版本：** 总是读取最新的已提交数据。
- **会加锁：** 执行当前读的语句会获取锁，以防止其他事务进行修改，从而保证数据的一致性。
- **阻塞：** 当前读可能会阻塞其他试图获取相同数据行写锁的事务。

**触发当前读的操作：** 以下 SQL 语句会触发当前读：
- **`SELECT ... FOR UPDATE`**: 读取数据并获取行级排他锁（写锁）。其他事务不能再获取相同行的排他锁或共享锁。
- **`SELECT ... LOCK IN SHARE MODE`**: 读取数据并获取行级共享锁（读锁）。其他事务可以继续获取相同行的共享锁，但不能获取排他锁。
- **`INSERT`**: 插入新记录时，会尝试获取插入位置的排他锁。
- **`UPDATE`**: 更新现有记录时，会获取被更新行的排他锁。
- **`DELETE`**: 删除记录时，会获取被删除行的排他锁。

**适用场景：** 需要确保读取的数据是最新的，并且在当前事务操作期间数据不被其他事务修改的场景，例如执行更新、删除操作前需要先读取最新的数据。




| 特性         | 一致性快照读 (Consistent Snapshot Read) | 当前读 (Current Read) |
|--------------|---------------------------------------|-----------------------|
| 读取版本     | 历史版本（基于隔离级别）              | 最新已提交版本        |
| 是否加锁     | 否                                    | 是                    |
| 是否阻塞写   | 否                                    | 是（可能阻塞其他写操作）|
| 触发语句     | 普通 `SELECT` (在 RC 和 RR 下)        | `SELECT ... FOR UPDATE`，`SELECT ... LOCK IN SHARE MODE`，`INSERT`，`UPDATE`，`DELETE` |
| 主要目的     | 提高并发性，读取数据在某个时间点的一致性视图 | 保证读取最新数据并防止并发修改 |


## 6. MVCC 适用于哪些语句？

| 类型     | 是否走 MVCC？ | 说明                     |
|----------|-------|--------------------------|
| `SELECT` | 是   | 快照读，一致性视图       |
| `UPDATE` | 否   | 当前读，会加锁            |
| `DELETE` | 否   | 当前读，会加锁            |
| `SELECT ... LOCK IN SHARE MODE / FOR UPDATE` | 否 | 当前读，阻塞写操作   |




---

## 7. Undo Log 行版本保存位置

当你更新或删除一行时，InnoDB 会将旧值写入 Undo Log：

```text
原始记录: id=1, name='Tom'

UPDATE user SET name='Jerry' WHERE id=1;
→ Undo Log: 保存 name='Tom' 的旧版本
```

- Undo Log 保存在 **回滚段（rollback segment）**
- 通过 `DB_ROLL_PTR` 可链式访问多个旧版本

---


## 8. 常见问题

### MVCC 为什么只能在 **REPEATABLE READ** 下才能实现“可重复读”？

- 在 `REPEATABLE READ` 下，同一事务多次执行查询使用同一个 **Read View**
- 在 `READ COMMITTED` 下，每次查询都会创建新 Read View，所以数据可能变化

### 大事务会不会导致 Undo Log 过多？

是的，**长事务**会保留大量 Undo 数据，影响性能甚至造成回滚段膨胀。

---

## 9. MVCC 与锁的关系

| 类型           | 是否用锁？ | 是否用 MVCC？ |
|----------------|------|----------|
| 快照读（普通 SELECT） | 不加锁 | 用       |
| 当前读（UPDATE/DELETE） | 加锁  | 不用      |
| SELECT ... FOR UPDATE | 加锁  | 不用      |

> MVCC ≠ 不加锁。它是对“读”的优化机制，写操作仍需加锁。

---

