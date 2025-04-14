# Mysql基础-锁


## 1. 锁的分类

| 维度           | 分类                          | 示例/说明                                  |
|----------------|-------------------------------|---------------------------------------------|
| **按对象粒度** | 表级锁、行级锁、页级锁         | 表锁/元数据锁 vs 行锁                       |
| **按行为类型** | 读锁（共享锁）、写锁（排它锁） | `SELECT ... LOCK IN SHARE MODE` / `FOR UPDATE` |
| **按加锁方式** | 显式锁、隐式锁、自旋锁        | 显式 `LOCK TABLE`；隐式 `InnoDB` 自动加锁     |
| **按实现层级** | MySQL 层锁、InnoDB 层锁        | 元数据锁、意向锁、行锁、间隙锁等            |

---

## 2. 表级锁（Table Lock）

**适用于：MyISAM、MEMORY、DDL 操作等**

- **共享锁（READ）**：多个线程可以同时读
- **排它锁（WRITE）**：独占整个表，其他线程不能读写

### 显式加锁示例：
```sql
LOCK TABLES my_table WRITE;
-- ... 操作 ...
UNLOCK TABLES;
```

表锁适合读多写少的场景，写操作会导致其他连接阻塞。

---

## 3. 行级锁（Row Lock）

**适用于：InnoDB，粒度细，支持并发高**

### 行锁类型：

| 类型        | 描述                                 |
|-------------|--------------------------------------|
| **共享锁（S）** | 允许其他事务也加共享锁，不能加排它锁       |
| **排它锁（X）** | 阻止其他事务对该行加任何类型的锁         |

### 示例：

```sql
-- 加共享锁
SELECT * FROM user WHERE id = 1 LOCK IN SHARE MODE;

-- 加排它锁（推荐用于更新前锁定）
SELECT * FROM user WHERE id = 1 FOR UPDATE;
```

行锁依赖索引，**不走索引时会锁全表！**

---

## 4. 意向锁（Intention Lock）

**作用：加速表锁与行锁的兼容性检查，自动由 InnoDB 添加**

| 类型       | 含义                             |
|------------|----------------------------------|
| **IS**     | 意向共享锁（事务打算对某些行加共享锁） |
| **IX**     | 意向排它锁（事务打算对某些行加排它锁） |

示例：如果你对一行加了排它锁，InnoDB 会自动给表加一个 IX 锁。

---

## 5. 间隙锁、临键锁、Next-Key Lock

这些是 **InnoDB 为了解决幻读** 引入的特殊锁类型，**仅在 `REPEATABLE READ` 下生效，配合 MVCC 实现事务隔离。**

| 类型           | 锁住对象                     | 用途                           |
|----------------|------------------------------|--------------------------------|
| **间隙锁**     | 某两个索引值之间的“空隙”       | 防止插入“幻影行”                |
| **记录锁**     | 单个索引记录（行锁）          | 普通行锁                       |
| **Next-Key锁** | 记录锁 + 间隙锁                | 解决幻读，避免插入               |


### 5.1 记录锁（Record Lock）

- 锁住**某一条真实存在的记录行**。
- 实质就是我们常说的 **行锁**。

#### 示例：
```sql
SELECT * FROM users WHERE id = 10 FOR UPDATE;
```
此语句会给 `id = 10` 的那一行加一个记录锁。

---

### 5.2 间隙锁（Gap Lock）

- 锁住的是两个索引值之间的“空隙”（**不包括这两个值本身**）。
- 作用是：**防止其他事务在该“间隙”中插入新记录**，从而避免“幻影行”出现。

#### 示例：
表中 `id` 字段为主键，现有数据：

```
id: 10, 20, 30
```

```sql
SELECT * FROM users WHERE id > 10 AND id < 30 FOR UPDATE;
```

将会加锁：

- `id = 20`：记录锁
- `(10, 20)`：间隙锁
- `(20, 30)`：间隙锁  
这样别人就不能在 10 和 30 之间插入新的记录。

---

### 5.3 Next-Key Lock（临键锁）

- InnoDB 默认采用的是这种锁。
- 本质上是：**记录锁 + 紧邻前面的间隙锁**。

#### 举例说明：

当前有索引字段如下：
```
索引列值：5, 10, 15, 20, 25
```

执行：
```sql
SELECT * FROM users WHERE age = 15 FOR UPDATE;
```

InnoDB 实际加的锁是：
```
(10,15]  → 也就是锁住间隙(10,15) + 行15（记录锁）
```

> 这就是 Next-Key Lock：**左开右闭区间**。

#### 如果你加的是范围查询：

```sql
SELECT * FROM users WHERE age > 10 AND age < 20 FOR UPDATE;
```

加锁范围：
- 间隙 `(10,15)`
- 行锁 `15`
- 间隙 `(15,20)`

**总结：锁住范围 (10,20)，包括中间所有的间隙与记录。**

---

### 5.4 唯一索引的优化：行锁替代 Next-Key Lock

InnoDB 在遇到**唯一索引 + 等值查询**时，会**降级为纯记录锁**，不加间隙锁或 Next-Key Lock。

```sql
-- 假设 age 是唯一索引
SELECT * FROM users WHERE age = 15 FOR UPDATE;
```

此时 InnoDB 只加：
- `age = 15` 的记录锁，不会锁间隙 `(10,15)`。

**性能优化技巧：加唯一索引，减少锁范围！**

---

### 5.5 SHOW ENGINE INNODB STATUS 查看锁

执行完加锁语句后可查看当前锁信息：
```sql
SHOW ENGINE INNODB STATUS\G
```

可以看到类似：
```
Record lock, heap no 3 PHYSICAL RECORD: n_fields 1; compact format;...
```

其中包含：
- `Record lock`: 记录锁
- `Gap lock`: 间隙锁
- `Next-Key lock`: 临键锁





---

## 6. 元数据锁（Metadata Lock，MDL）

**MySQL 层的锁**，保护表结构的元数据。

- SELECT 时会加 **共享 MDL**
- ALTER TABLE 时会加 **排它 MDL**

示例：
```sql
-- A线程
SELECT * FROM user WHERE id = 1;

-- B线程（会阻塞）
ALTER TABLE user ADD COLUMN age INT;
```

所以：**长时间的 SELECT 会阻塞 DDL！**

---

## 7. 死锁与排查

### 死锁产生条件（四个必要条件）：
1. 互斥
2. 占有且等待
3. 非抢占
4. 循环等待

### 检查死锁：
```sql
SHOW ENGINE INNODB STATUS\G
```

InnoDB 会自动检测死锁，并选择回滚代价小的事务。

---

## 8. 加锁策略建议

| 操作类型         | 加锁建议                                             |
|------------------|------------------------------------------------------|
| 精确更新         | 使用 `FOR UPDATE` 明确加排它锁                       |
| 范围删除         | 配合索引，否则锁全表                                 |
| 批量插入         | 避免间隙锁影响，可考虑降级隔离级别或使用唯一索引     |
| 高并发查询       | 控制事务时间，避免长时间持锁                         |
| 表结构频繁修改   | 避免与大量 SELECT 同时进行，使用低峰期操作            |

---

## 9. 常见锁状态监控

```sql
-- 查看锁等待信息（performance_schema）
SELECT * FROM performance_schema.data_locks;

-- 查看 InnoDB 锁等待
SHOW ENGINE INNODB STATUS;
```

---

## 10. 事务中的锁行为示例

事务A：
```sql
BEGIN;
SELECT * FROM user WHERE id = 1 FOR UPDATE;
-- 等待提交
```

事务B：
```sql
UPDATE user SET name = 'x' WHERE id = 1;
-- 阻塞，直到事务A提交
```

---

