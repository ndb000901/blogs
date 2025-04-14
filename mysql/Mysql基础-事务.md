# Mysql基础-事务

**事务** 是一组操作的集合，这些操作要么全部执行成功，要么全部回滚。事务确保了数据库操作的**原子性**、**一致性**、**隔离性**和**持久性**（ACID特性）。MySQL 使用 InnoDB 存储引擎时，默认支持事务。

## 1. **ACID 特性**

事务的四个基本特性分别是：
- **原子性（Atomicity）**：事务中的所有操作要么全部成功，要么全部失败。即使事务执行中发生错误，也会回滚到事务开始之前的状态。
- **一致性（Consistency）**：事务执行前后，数据库的状态应该保持一致，不会留下不完整的数据。
- **隔离性（Isolation）**：事务之间的执行不会互相干扰，每个事务都像是独立执行的。
- **持久性（Durability）**：一旦事务提交，对数据库的修改是永久的，即使系统崩溃也能保证数据不丢失。

## 2. **事务的基本操作**

MySQL 中的事务由以下几种基本操作组成：

- **START TRANSACTION**：开启一个事务。
- **COMMIT**：提交事务，使所有更改永久保存。
- **ROLLBACK**：回滚事务，撤销事务中做的所有修改。
- **SAVEPOINT**：设置保存点，可以在回滚时选择性地回滚到某个保存点。
- **SET AUTOCOMMIT**：设置是否自动提交，默认为 `1`（自动提交）。

## 3. **事务的隔离级别**

MySQL 支持四种事务隔离级别，隔离级别决定了事务对其他事务的可见性。MySQL 默认的存储引擎 InnoDB 支持这些隔离级别。

### **READ UNCOMMITTED（读未提交）**：

- 事务可以读取另一个未提交事务的数据（脏读）。
- 最低级别的隔离级别，可能导致脏读、不可重复读、幻读。

### **READ COMMITTED（读已提交）**：

- 事务只能读取其他事务已提交的数据。
- 可以避免脏读，但仍然可能出现不可重复读和幻读。

### **REPEATABLE READ（可重复读）**：

- 事务在开始后，会多次读取同一数据时，始终返回相同的值。
- 可以避免脏读和不可重复读，但仍然可能出现幻读。

### **SERIALIZABLE（可串行化）**：

- 最严格的隔离级别，事务按照顺序执行，完全避免脏读、不可重复读和幻读。
- 性能开销最大。

### 设置隔离级别

```sql
SET TRANSACTION ISOLATION LEVEL [LEVEL];
```

例如：

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

也可以在会话级别或全局级别设置默认的隔离级别：

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

### 查看隔离级别

```sql
SELECT @@transaction_isolation;
```

**可能返回的值包括：**

* `READ-UNCOMMITTED`
* `READ-COMMITTED`
* `REPEATABLE-READ` (MySQL 的默认隔离级别)
* `SERIALIZABLE`

## 4. **事务的实现**

事务的实现依赖于数据库管理系统（DBMS）如何处理数据的持久性、隔离性以及如何保证原子性和一致性。MySQL 中使用的 `InnoDB` 存储引擎通过以下方式实现事务：

- **重做日志（Redo Log）**：记录事务的所有更改，以便在系统崩溃后能够恢复数据。
- **回滚日志（Undo Log）**：记录事务未提交前的状态，以便在事务回滚时恢复数据。
- **锁机制**：通过行级锁和表级锁来保证事务的隔离性。

## 5. **事务的锁机制**

InnoDB 提供了多种锁类型来实现事务的隔离性：
- **共享锁（S Lock）**：多个事务可以持有共享锁，它允许事务读取数据，但不能修改数据。
- **排他锁（X Lock）**：当事务持有排他锁时，其他事务不能对数据进行读或写操作。通常用于更新或删除数据。

事务会自动加锁，并且根据隔离级别选择不同的锁策略。

## 6. **事务的实际应用场景**

1. **银行转账操作**
   在银行转账操作中，需要确保转账的两次操作（从账户 A 扣款、向账户 B 存款）要么同时成功，要么同时失败。例如，如果从账户 A 扣款成功，但向账户 B 存款失败，应该回滚整个事务。

   ```sql
   START TRANSACTION;
   UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
   UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
   COMMIT;
   ```

2. **订单处理**
   在订单处理中，确保订单创建和库存扣减是一个原子操作。如果任何一步失败，应该回滚整个事务，确保数据一致性。

   ```sql
   START TRANSACTION;
   INSERT INTO orders (order_id, customer_id, total_price) VALUES (1001, 1, 500);
   UPDATE inventory SET stock = stock - 1 WHERE product_id = 123;
   COMMIT;
   ```

3. **购物车操作**
   在电子商务网站上，用户的购物车操作涉及到商品库存的扣减、订单的创建等，通常会使用事务保证这些操作的一致性。

   ```sql
   START TRANSACTION;
   INSERT INTO shopping_cart (user_id, product_id, quantity) VALUES (1, 123, 2);
   UPDATE inventory SET stock = stock - 2 WHERE product_id = 123;
   COMMIT;
   ```

## 7. **事务的回滚与提交**

- **回滚（ROLLBACK）**：
  如果事务中的某个操作出现错误或遇到异常，使用 `ROLLBACK` 回滚事务，撤销所有已执行的操作。

   ```sql
   START TRANSACTION;
   UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
   -- 如果出现错误，可以回滚
   ROLLBACK;
   ```

- **提交（COMMIT）**：
  如果事务中的所有操作都成功执行，使用 `COMMIT` 提交事务，确认所有操作对数据库的修改。

   ```sql
   START TRANSACTION;
   UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
   UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
   COMMIT;
   ```

## 8. **事务相关的状态监控**

MySQL 提供了以下命令来查看当前的事务状态和相关信息：

### 查看当前事务状态：

```sql
SHOW ENGINE INNODB STATUS;
```

### 查看锁相关信息：

```sql
SHOW OPEN TABLES WHERE In_use > 0;
```

### 查看当前的事务日志（日志文件）：

```sql
SHOW BINARY LOGS;
```

---
