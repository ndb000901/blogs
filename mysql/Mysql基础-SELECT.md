# Mysql基础-SELECT


## 1. 基础语法

```sql
SELECT [列名|*]
FROM 表名
[WHERE 条件]
[GROUP BY 分组字段]
[HAVING 条件]
[ORDER BY 排序字段 [ASC|DESC]]
[LIMIT [偏移量,] 行数];
```

### 示例：
```sql
SELECT id, name FROM users
WHERE status = 'active'
ORDER BY created_at DESC
LIMIT 10;
```

---

## 2. 常见用法

### SELECT 指定列
```sql
SELECT name, age FROM students;
```

### SELECT 所有列
```sql
SELECT * FROM students;
```

---

## 3. 条件查询 `WHERE`

支持：
- 比较运算：`=, !=, >, <, >=, <=`
- 逻辑运算：`AND`, `OR`, `NOT`
- 范围/集合判断：`BETWEEN`, `IN`, `IS NULL`, `LIKE`

```sql
SELECT * FROM orders
WHERE status = 'completed' AND total > 100;
```

---

## 4. 排序 `ORDER BY`

```sql
SELECT * FROM products
ORDER BY price DESC, created_at ASC;
```

---

## 5. 限制结果数量 `LIMIT`

```sql
-- 前 10 条
SELECT * FROM logs LIMIT 10;

-- 跳过前 20 条，取 10 条
SELECT * FROM logs LIMIT 20, 10;
```

---

## 6. 去重 `DISTINCT`

```sql
SELECT DISTINCT city FROM customers;
```

---

## 7. 分组 `GROUP BY` + `HAVING`

```sql
-- 每个城市的用户数
SELECT city, COUNT(*) AS user_count
FROM users
GROUP BY city;

-- 只查用户数大于 100 的城市
SELECT city, COUNT(*) AS user_count
FROM users
GROUP BY city
HAVING user_count > 100;
```

---

## 8. 聚合函数（统计类函数）

| 函数        | 含义                     |
|-------------|--------------------------|
| `COUNT(*)`  | 总数                     |
| `SUM(col)`  | 求和                     |
| `AVG(col)`  | 平均值                   |
| `MAX(col)`  | 最大值                   |
| `MIN(col)`  | 最小值                   |

```sql
SELECT COUNT(*) FROM logs WHERE level = 'ERROR';
```

---

## 9. 子查询（Subquery）

### 作为条件
```sql
SELECT * FROM products
WHERE id IN (
  SELECT product_id FROM orders WHERE user_id = 10
);
```

### 作为字段
```sql
SELECT name, (
  SELECT COUNT(*) FROM orders o WHERE o.user_id = u.id
) AS order_count
FROM users u;
```

---

## 10. 连接查询 `JOIN`

### INNER JOIN（内连接）
```sql
SELECT u.name, o.amount
FROM users u
JOIN orders o ON u.id = o.user_id;
```

### LEFT JOIN（左连接）
```sql
SELECT u.name, o.amount
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

---

## 11. 窗口函数（MySQL 8 支持）

```sql
SELECT name, department,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rk
FROM employees;
```

---

## 12. JSON 查询（MySQL 5.7+ 支持）

```sql
SELECT json_column->>'$.name' AS name
FROM json_table;
```

---

## 13. WITH 子句（公共表达式 CTE）

```sql
WITH recent_orders AS (
  SELECT * FROM orders WHERE created_at > NOW() - INTERVAL 30 DAY
)
SELECT user_id, COUNT(*) FROM recent_orders GROUP BY user_id;
```

---

## 14. 性能优化建议

| 技巧                        | 原因                                                    |
|-----------------------------|-----------------------------------------------------------|
| 使用 `LIMIT` 限制行数      | 避免全表扫描过大返回                                     |
| 给 `WHERE` 条件加索引      | 提高检索速度，避免全表扫描                               |
| 避免 `SELECT *`            | 明确列名更高效，节省网络传输                             |
| `EXPLAIN SELECT ...` 分析计划 | 检查索引使用和连接顺序，辅助优化查询逻辑                 |
| 使用覆盖索引                | 查询字段只包含索引列时，性能最佳（避免回表）              |
| 利用分区表或分库分表       | 针对超大表可考虑物理结构优化                             |

---
