# Mysql函数-LockingFunctions

MySQL 8 中的 **Locking Functions（锁函数）** 主要用于 **控制并发访问资源或数据**，提供了多个内置函数来实现 **应用级锁（advisory locks）**。这些函数与数据库事务锁（如行锁、表锁）不同，是**用户自定义的锁机制**，常用于协调跨事务、跨连接的同步操作。

---

## 1. 常用 Locking Functions

| 函数名 | 描述 |
|--------|------|
| `GET_LOCK(name, timeout)` | 请求一个命名锁 |
| `RELEASE_LOCK(name)` | 释放命名锁 |
| `IS_FREE_LOCK(name)` | 检查某锁是否可用 |
| `IS_USED_LOCK(name)` | 查看谁持有某锁（线程 ID） |
| `NAME_CONST(name, value)` | 通常用于与锁函数组合，给列别名（不常用） |

---

## 2. 锁函数示例

---

### 2.1 `GET_LOCK(name, timeout)`
尝试获取一个命名锁（应用层锁）。超时未获取返回 `NULL` 或 `0`。

```sql
-- 获取名为 'mylock' 的锁，等待最多 10 秒
SELECT GET_LOCK('mylock', 10);
-- 返回 1 表示成功，0 表示超时，NULL 表示错误
```

>  **锁是基于连接的**：断开连接会自动释放锁。

---

### 2.2 `RELEASE_LOCK(name)`
释放当前连接持有的指定锁。

```sql
SELECT RELEASE_LOCK('mylock');
-- 返回 1 表示成功释放，0 表示当前连接未持有该锁，NULL 表示错误
```

---

### 2.3 `IS_FREE_LOCK(name)`
判断某个锁是否空闲。

```sql
SELECT IS_FREE_LOCK('mylock');
-- 返回 1 表示未被持有，0 表示已被别人持有，NULL 表示错误
```

---

### 2.4 `IS_USED_LOCK(name)`
返回持有指定锁的线程 ID。

```sql
SELECT IS_USED_LOCK('mylock');
-- 返回 thread_id，如果未持有返回 NULL
```

可以根据 `SHOW PROCESSLIST` 或 `INFORMATION_SCHEMA.PROCESSLIST` 查询对应连接。

---

### 注意事项

- 每个连接 **最多只能持有一个命名锁**，第二次 `GET_LOCK()` 会释放之前的锁。
- 不同连接之间锁是独立的，类似 **`mutex` 行为**。
- 不同数据库实例之间锁是无效的（不是分布式锁）。

---

## 3. 典型使用场景

### 跨表资源互斥
多个表需要同时更新，但事务不便覆盖时，可使用 GET_LOCK 保证互斥。

```sql
-- BEGIN
SELECT GET_LOCK('update-user-and-logs', 5);
-- 执行多个表更新操作
-- COMMIT
SELECT RELEASE_LOCK('update-user-and-logs');
```

---

### 作为简易分布式锁（单实例）
适合小型项目中做简单互斥，不建议用于高并发、大规模服务。

---

### 避免重复执行任务
```sql
-- 确保一个定时任务不会被多次执行
IF GET_LOCK('cron-task:cleanup', 0) THEN
  -- 执行任务
  -- ...
  DO RELEASE_LOCK('cron-task:cleanup');
END IF;
```

---






