# Mysql基础-临时表


## 1. 临时表

临时表（`TEMPORARY TABLE`）是：

- 在当前会话中**临时存在**的表；
- 只能被**创建它的 session 使用**；
- 会话断开或显式 `DROP` 后自动销毁；
- 可用于存储中间结果、优化复杂查询、避免重复计算。

---

## 2. 创建临时表

### 2.1 创建结构

```sql
CREATE TEMPORARY TABLE temp_users (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

与普通表一样创建，只是多了 `TEMPORARY` 关键字。

---

### 2.2 创建并插入查询结果

```sql
CREATE TEMPORARY TABLE temp_orders AS
SELECT order_id, user_id, total_amount
FROM orders
WHERE created_at >= CURDATE();
```

用于中间结果缓存，后续可继续 join、聚合等操作。

---

## 3. 使用临时表

```sql
SELECT * FROM temp_orders;
```

就像普通表一样使用。

---

## 4. 删除临时表

```sql
DROP TEMPORARY TABLE temp_orders;
```

也可以：

```sql
DROP TABLE temp_orders;
```

注意：**`DROP` 普通表时不会误删临时表**，反之亦然。

---

## 5. 作用域 & 生命周期

| 特性           | 描述                                  |
|----------------|----------------------------------------|
| 会话级         | 只对当前连接可见，**其他连接不可访问** |
| 自动销毁       | 会话断开或连接关闭时自动删除           |
| 同名不冲突     | 可以和正式表同名（临时表优先生效）     |

例如：

```sql
CREATE TABLE users (...);
CREATE TEMPORARY TABLE users (...);
SELECT * FROM users; -- 实际访问的是临时表！
```

---

## 6. 应用场景

### 缓存中间结果（避免重复计算）

```sql
CREATE TEMPORARY TABLE tmp_top_users AS
SELECT user_id, COUNT(*) AS login_count
FROM user_logins
GROUP BY user_id
HAVING login_count > 10;

SELECT * FROM tmp_top_users WHERE user_id IN (SELECT user_id FROM black_list);
```

### 分步骤处理复杂逻辑

```sql
-- 第一步：获取昨天的活跃用户
CREATE TEMPORARY TABLE active_users AS
SELECT user_id FROM events WHERE event_date = CURDATE() - INTERVAL 1 DAY;

-- 第二步：从订单中过滤这些用户
SELECT *
FROM orders
WHERE user_id IN (SELECT user_id FROM active_users);
```

### 用于分页临时缓存

```sql
CREATE TEMPORARY TABLE temp_result AS (
    SELECT * FROM orders WHERE status = 'paid'
);

SELECT * FROM temp_result ORDER BY created_at DESC LIMIT 10;
```

---

## 7. 临时表的限制

| 限制项                     | 说明                                       |
|----------------------------|--------------------------------------------|
| 仅当前连接可见             | 无法跨连接共享                             |
| 不支持 `FOREIGN KEY`       | 临时表不能设置外键                        |
| 不持久化                   | 重启后不保留                              |
| 不记录 binlog              | 默认不记录 binlog，除非显式设置           |
| `CREATE TEMPORARY TABLE ... LIKE` | 不支持复制 foreign key 结构             |

---

## 8. 临时表 vs 派生表（子查询）

| 对比项          | 临时表                          | 派生表（子查询）                |
|------------------|----------------------------------|--------------------------------|
| 可复用性        | 可多次查询                       | 每次使用都会重新执行            |
| 是否命名        | 明确命名                        | 无法复用，别名仅当前使用有效      |
| 可否建索引      | 支持建索引优化查询                  | 不可                            |
| 生命周期        | 当前连接                        | 当前语句                          |

---

## 9. 性能优化

| 建议                          | 说明                                       |
|-------------------------------|--------------------------------------------|
| 尽量避免大字段/大量数据临时表 | 否则可能溢出到磁盘                         |
| 使用 `ENGINE=MEMORY` 提速     | 临时表默认使用 `InnoDB`，可手动设为 `MEMORY` |
| 合理建索引                   | 临时表也可以加索引提升复杂查询性能         |

示例：

```sql
CREATE TEMPORARY TABLE fast_tmp (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    INDEX(name)
) ENGINE=MEMORY;
```

---

## 10. MySQL 内部自动临时表

当你执行以下查询时，**MySQL 自动创建临时表**：

- `GROUP BY` / `DISTINCT`
- `ORDER BY` + `LIMIT`，且不能用索引优化时
- 包含子查询、派生表、大结果集等

可用 `EXPLAIN` 查看是否触发了 `Using temporary`：


