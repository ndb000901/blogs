# Mysql基础-隔离级别

事务隔离级别是指数据库管理系统（DBMS）在处理多个事务时，如何隔离它们的执行，确保事务之间的相互影响最小化。隔离级别的设置决定了在事务并发执行时，一个事务能看到另一个事务的哪些数据变更。不同的隔离级别通过不同程度的锁和控制来解决并发事务中的潜在问题。

MySQL 支持四种标准的事务隔离级别：

- **READ UNCOMMITTED**
- **READ COMMITTED**
- **REPEATABLE READ** 
- **SERIALIZABLE**

每种隔离级别都对事务并发的控制和性能产生不同的影响

## 1. 问题

在数据库事务的隔离性方面，**脏读**、**不可重复读**和**幻读**是常见的并发问题。它们描述了在不同事务隔离级别下，事务之间的并发操作可能导致的读取不一致的问题。

### 1.1 **脏读（Dirty Read）**

#### 定义：
脏读是指事务 A 读取了事务 B 尚未提交的数据。如果事务 B 最终回滚，那么事务 A 读取的数据将是无效的，可能导致数据不一致性。

#### 举例：
- 事务 A 启动，并且查询一个数据记录。
- 事务 B 对这个记录进行了更新，但还未提交。
- 事务 A 读取了这个未提交的值（脏读），并且继续执行。
- 事务 B 随后回滚，这时事务 A 读取到的数据是不正确的。

#### 影响：
- 脏读会导致事务 A 获取到无效数据，这可能对业务逻辑产生不利影响，造成数据不一致。

#### 解决方案：
- 通过设置适当的事务隔离级别来避免脏读，尤其是使用 `READ COMMITTED` 或更高的隔离级别可以有效避免脏读。

### 1.2 **不可重复读（Non-repeatable Read）**

#### 定义：
不可重复读指的是事务 A 在执行过程中读取了某个数据项，随后又再次读取该数据项，但此时该数据项已经被事务 B 修改或删除。这种情况会导致事务 A 在同一事务中多次读取到不同的值。

#### 举例：
- 事务 A 启动，查询某条数据（如，查询某个账户的余额为 100）。
- 事务 B 对该账户的余额进行了更新（将余额改为 150），并提交了事务。
- 事务 A 再次查询该账户余额时，发现余额已经发生了变化（变为 150），这就出现了不可重复读的情况。

#### 影响：
- 不可重复读会导致事务 A 中的两个读取操作之间的不一致，从而影响数据的准确性和业务逻辑。

#### 解决方案：
- 可以通过设置 `REPEATABLE READ` 或更高的隔离级别来避免不可重复读。`REPEATABLE READ` 会锁定事务读取的记录，避免其他事务修改它们。

### 1.3 **幻读（Phantom Read）**

#### 定义：
幻读是指在事务执行过程中，另一个事务插入、更新或删除了数据，使得事务 A 读取的数据集发生了变化，导致事务 A 在不同的查询中看到不同的数据集合。换句话说，事务 A 在同一事务中执行两次相同的查询时，查询结果集发生了变化。

#### 举例：
- 事务 A 启动并执行一个查询，查找某个范围内的记录。
- 事务 B 在这个范围内插入了新的数据，并提交。
- 事务 A 再次执行相同的查询，发现结果集发生了变化，新增的记录就像“幻影”一样出现了。

#### 影响：
- 幻读可能导致数据不一致，特别是在涉及到报告生成、总计计算等操作时，数据集的变化会影响结果的正确性。

#### 解决方案：
- 为了避免幻读，可以使用 **Serializable** 隔离级别。`Serializable` 会在事务期间对涉及的所有记录加锁，从而阻止其他事务插入或删除满足条件的记录。

---

## 2. 隔离级别

### 2.1 **READ UNCOMMITTED（读未提交）**

**特性**：
- 允许事务读取其他未提交事务的数据，发生**脏读**。
- 是最低的隔离级别，性能较好，但并发控制最弱。

**问题**：
- **脏读**：一个事务读取到另一个事务未提交的数据，可能会在另一个事务回滚时导致数据不一致。
- **不可重复读**：事务中多次读取同一数据，结果可能不同。
- **幻读**：查询的结果集在事务执行过程中可能发生变化。

**适用场景**：
- 当对数据一致性要求不高，且对性能要求很高的场景。

**例子**：
事务 A 正在更新某条记录，但尚未提交，事务 B 就能读取到事务 A 未提交的数据。

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

-- 事务 A
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;

-- 事务 B
START TRANSACTION;
SELECT * FROM accounts WHERE account_id = 1;  -- 事务 B 能看到事务 A 未提交的数据
```

### 2.2 **READ COMMITTED（读已提交）**

**特性**：
- 事务只能读取到已提交事务的数据，避免了**脏读**。
- 但可能会发生**不可重复读**和**幻读**。

**问题**：
- **不可重复读**：在一个事务中对同一数据进行多次读取时，数据可能发生变化，因为其他事务可以在该事务提交前修改数据。
- **幻读**：事务读取的范围内，其他事务可以插入新的数据，导致查询结果发生变化。

**适用场景**：
- 数据一致性要求高，但对性能要求较高的场景。适用于避免脏读，但可容忍某些不一致现象。

**例子**：
事务 A 执行查询后，事务 B 更新了相同的数据，再次查询时事务 A 看到的数据发生变化。

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- 事务 A
START TRANSACTION;
SELECT * FROM accounts WHERE account_id = 1;

-- 事务 B
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;  -- 修改数据

-- 事务 A
SELECT * FROM accounts WHERE account_id = 1;  -- 事务 A 看到修改后的数据
```

### 2.3 **REPEATABLE READ（可重复读）**

**特性**：
- 事务在开始后，多次读取同一数据时，始终返回相同的值。
- 可以避免**脏读**和**不可重复读**。
- 可能会发生**幻读**，因为查询的范围内其他事务仍然可以插入新的数据。

**问题**：
- **幻读**：事务在执行过程中，查询的结果集可能因为其他事务的插入操作而发生变化。尽管当前数据的一致性得到了保证，但结果集的范围可能不一致。

**适用场景**：
- 保证事务在执行期间数据的稳定性，但容忍一些插入操作（如幻读）。

#### 表结构

```sql

CREATE TABLE `user` (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `name` varchar(64) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `age` int DEFAULT NULL,
  `email` varchar(64) COLLATE utf8mb4_general_ci DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=118 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci

INSERT INTO `user` (`id`, `name`, `age`, `email`) VALUES
(1, 'user1', 18, 'user1@gmail.com'),
(101, 'user-tx', 101, 'user-tx@gmail.com');
```

#### 示例1:

```sql

# 事务A
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SELECT @@transaction_isolation;
START TRANSACTION
select * from user; 
# 首次select， 结果
# (1, 'user1', 18, 'user1@gmail.com'),
# (101, 'user-tx', 101, 'user-tx@gmail.com');

# 事务B
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SELECT @@transaction_isolation;
START TRANSACTION;
insert into user(name, age, email) VALUES ('t1', 200, 't1@gmail.com');
COMMIT;

# 事务A
select * from user; 
# 看到的结果与第一次结果一致
# (1, 'user1', 18, 'user1@gmail.com'),
# (101, 'user-tx', 101, 'user-tx@gmail.com');
# 普通select 使用快照读，使用的是首次select 创建的Read View

# 以下操作使用当前读(读取数据库最新版本), 会看到新插入的行
# (1, 'user1', 18, 'user1@gmail.com'),
# (101, 'user-tx', 101, 'user-tx@gmail.com');
# (118, 't1', 200, 't1@gmail.com');
select * from user for update;
select * from user lock in share mode ;
update ....
delete ....
insert ..

```

### 2.4 **SERIALIZABLE（可串行化）**

**特性**：
- 最严格的事务隔离级别，事务按顺序执行，避免了所有并发问题。
- 强制事务串行执行，事务之间不会发生交叉操作。
- 事务会加锁，确保每个事务都能看到其它事务提交的数据。

**问题**：
- **性能开销最大**，因为它会使用大量的锁，可能导致**死锁**和**事务等待**，从而影响并发性能。
- 在高并发场景下，可能导致大量的阻塞。

**适用场景**：
- 数据一致性要求极高的场景，适用于对并发性能要求较低的事务。

**例子**：
事务 A 执行查询时，事务 B 需要等待事务 A 完成才能执行，事务 A 看到的始终是自己提交之前的数据。

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- 事务 A
START TRANSACTION;
SELECT * FROM accounts WHERE account_id = 1;

-- 事务 B
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;  -- 事务 B 被阻塞，直到事务 A 完成
```

##  **隔离级别对性能和一致性的影响**

| 隔离级别                | 脏读   | 不可重复读   | 幻读   | 并发性能     |
|----------------------|--------|--------------|--------|--------------|
| **READ UNCOMMITTED**  | 允许   | 允许         | 允许   | 最高         |
| **READ COMMITTED**    | 不允许 | 允许         | 允许   | 较高         |
| **REPEATABLE READ**   | 不允许 | 不允许       | 允许   | 中等         |
| **SERIALIZABLE**      | 不允许 | 不允许       | 不允许 | 最低         |

- **READ UNCOMMITTED** 提供最高的并发性能，但会允许所有类型的数据问题（脏读、不可重复读、幻读）。
- **READ COMMITTED** 会防止脏读，但允许不可重复读和幻读，性能相对较好。
- **REPEATABLE READ** 防止了脏读和不可重复读，但仍可能发生幻读，适合大多数业务场景。
- **SERIALIZABLE** 是最严格的隔离级别，提供最高的数据一致性保证，但对并发性能影响最大。

## 3. **设置隔离级别**

可以使用 `SET TRANSACTION ISOLATION LEVEL` 来设置事务的隔离级别，或者在数据库连接创建时设置默认隔离级别：

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

也可以在全局或会话级别设置隔离级别：

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

```sql
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

