# Mysql基础-UPDATE

## 1. UPDATE 基础语法

```sql
UPDATE 表名
SET 列1 = 值1, 列2 = 值2, ...
[WHERE 条件];
```

### 示例：
```sql
UPDATE users
SET name = '张三丰', email = 'zhangsan@example.com'
WHERE id = 1;
```

> **一定要加 `WHERE` 限制条件**，否则会更新整张表！

---

## 2. 更新多条记录

```sql
UPDATE users
SET status = 'inactive'
WHERE last_login < '2024-01-01';
```

> 支持批量更新：满足条件的多条记录会被一起修改。

---

## 3. 防止误操作技巧

### 3.1 开启安全更新模式

```sql
SET SQL_SAFE_UPDATES = 1;
```

> 没有 `WHERE` 或 `LIMIT` 时禁止执行 `UPDATE`。

### 3.2 加 `LIMIT` 限制更新行数（调试中非常有用）：

```sql
UPDATE users
SET status = 'testing'
WHERE status IS NULL
LIMIT 10;
```

---

## 4. 更新表达式支持运算与函数

```sql
UPDATE products
SET price = price * 1.1,  -- 涨价 10%
    updated_at = NOW();
```

支持各种运算：`+ - * /`、字符串拼接、函数调用（`NOW()`、`CONCAT()`、`IF()`等）

---

## 5. UPDATE + 子查询

### 使用 SELECT 查询值进行更新：

```sql
UPDATE employees e
SET e.salary = (
    SELECT AVG(salary)
    FROM employees
    WHERE department = e.department
)
WHERE e.role = '实习生';
```

### 使用 JOIN 进行关联更新：

```sql
UPDATE users u
JOIN user_scores s ON u.id = s.user_id
SET u.score = s.total_score
WHERE s.year = 2024;
```

> 推荐使用 JOIN，而不是子查询，在大数据量下效率更高。

---

## 6. UPDATE IGNORE 用法

忽略某些约束错误（如唯一键、外键）继续执行：

```sql
UPDATE IGNORE users
SET email = NULL
WHERE id = 1;
```

---

## 7. 结合事务使用

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;

COMMIT;
```


---

## 8. 性能优化建议

| 优化方法                       | 说明                                               |
|-------------------------------|----------------------------------------------------|
| 使用索引字段做条件            | `WHERE` 子句要走索引，否则全表扫描                |
| 使用 `LIMIT`+`ORDER BY` 分批更新 | 避免大事务锁表                                     |
| 拆分批量更新事务              | 尤其适用于 InnoDB，减少 undo log 与锁竞争         |
| 避免函数出现在 `WHERE` 中     | 例如 `WHERE DATE(update_time) = '2024-01-01'`，函数可能阻碍索引 |
| 避免一次更新全表              | 不加 `WHERE` 是高风险操作，应设置 SQL_SAFE_UPDATES |

---

## 9. UPDATE 常见错误

| 错误代码 | 含义说明                               |
|----------|----------------------------------------|
| 1054     | Unknown column — 字段名写错            |
| 1062     | Duplicate entry — 唯一索引冲突         |
| 1048     | Column cannot be null — 非空字段被更新为 null |
| 1093     | You can't specify target table for update in FROM clause — 子查询中不能更新当前表 |

---

