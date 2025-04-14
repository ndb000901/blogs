# Mysql函数-AggregateFunctions


## 1. Aggregate Function（聚合函数）

聚合函数是一类**将多行数据压缩为单一结果**的函数，常用于 `GROUP BY` 分组统计、分析型查询、窗口函数中。

---

## 2. 常见聚合函数

### 2.1 `COUNT([DISTINCT] expr)`
- **功能**：统计行数。
- **示例**：
  ```sql
  SELECT COUNT(*) FROM orders;
  SELECT COUNT(DISTINCT user_id) FROM orders;
  ```

- **注意事项**：
    - `COUNT(*)` 会统计包括 NULL 在内的所有行。
    - `COUNT(column)` 会跳过 NULL。
    - `COUNT(DISTINCT col1, col2)` MySQL 8 起支持多列。

---

### 2.2 `SUM([DISTINCT] expr)`
- **功能**：求和，常用于数值列。
- **示例**：
  ```sql
  SELECT SUM(price) FROM orders;
  SELECT SUM(DISTINCT price) FROM orders;
  ```

- **注意事项**：传入非数值会隐式转换，建议显式 cast。

---

### 2.3 `AVG([DISTINCT] expr)`
- **功能**：求平均值。
- **示例**：
  ```sql
  SELECT AVG(score) FROM exam_scores;
  ```

- **注意事项**：返回结果类型可能为 `DECIMAL`，涉及精度问题。

---

### 2.4 `MIN(expr)` / `MAX(expr)`
- **功能**：最小值 / 最大值。
- **示例**：
  ```sql
  SELECT MIN(created_at), MAX(created_at) FROM users;
  ```

- **注意事项**：支持日期、字符串、数值等类型。

---

### 2.5 `GROUP_CONCAT(expr [ORDER BY ...] [SEPARATOR str])`
- **功能**：将组内多个字符串连接成一个字符串。
- **示例**：
  ```sql
  SELECT GROUP_CONCAT(name ORDER BY name SEPARATOR ', ') FROM users GROUP BY team_id;
  ```

- **注意事项**：
    - 默认长度限制为 1024，可通过 `group_concat_max_len` 参数调整。
    - 超出限制的内容会被截断。

---

### 2.6 `JSON_ARRAYAGG(expr)` / `JSON_OBJECTAGG(key, value)`
- **功能**：MySQL 8 引入，聚合为 JSON 数组 / 对象。
- **示例**：
  ```sql
  SELECT JSON_ARRAYAGG(name) FROM users GROUP BY team_id;
  SELECT JSON_OBJECTAGG(id, name) FROM users;
  ```

- **适合场景**：用于 API 查询返回结构化结果（比如构建 JSON 响应体）。

---

## 3. 配合 GROUP BY 使用示例

```sql
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;
```

---

## 4. 配合窗口函数（OVER）使用（MySQL 8+）

聚合函数 + `OVER(PARTITION BY ...)` 可以在不合并行的情况下做聚合分析：

```sql
SELECT employee_id, department_id,
       COUNT(*) OVER (PARTITION BY department_id) AS dept_count
FROM employees;
```

- 不会合并多行，适合分析每一行在组中的情况。

---

## 5. 注意事项和优化建议

-  **索引不能优化聚合本身**，但能优化 WHERE / GROUP BY 的过滤。
-  使用 `GROUP BY` 过多时注意临时表和排序可能带来的性能影响。
-  `GROUP_CONCAT` 建议设置足够大的 `group_concat_max_len`。
-  `DISTINCT` 会带来额外去重开销，要谨慎使用。
-  对大表做聚合建议加上合适的分区表或中间表优化。

---

