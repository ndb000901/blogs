# Mysql基础-分组

## 1. GROUP BY

`GROUP BY` 用于将结果集**按照某个字段进行分组**，常和聚合函数（如 `COUNT()`、`SUM()`、`AVG()` 等）一起使用，对每组数据进行统计汇总。

---

## 2. 基本语法

```sql
SELECT column1, AGG_FUNC(column2)
FROM table
GROUP BY column1;
```

其中 `AGG_FUNC` 为聚合函数，例如：

- `COUNT()`：统计数量
- `SUM()`：求和
- `AVG()`：平均值
- `MAX()`：最大值
- `MIN()`：最小值

---

## 3. 示例

### 示例：统计每个部门的员工人数

```sql
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;
```

### 示例：统计每种商品的总销售额

```sql
SELECT product_id, SUM(price * quantity) AS total_sales
FROM orders
GROUP BY product_id;
```

---

## 4. 与 HAVING 联用（筛选分组后的结果）

`HAVING` 用于**筛选分组之后的聚合结果**，不同于 `WHERE` 是在分组前过滤。

### 示例：筛选销售额大于 10000 的商品

```sql
SELECT product_id, SUM(price * quantity) AS total_sales
FROM orders
GROUP BY product_id
HAVING total_sales > 10000;
```

---

## 5. GROUP BY 多字段（多列分组）

可以对多个字段分组，形成更精细的分组维度：

```sql
SELECT year, department_id, COUNT(*) AS staff_count
FROM employees
GROUP BY year, department_id;
```

---

## 6. GROUP BY + WITH ROLLUP（自动生成小计、总计）

```sql
SELECT department_id, COUNT(*) AS staff_count
FROM employees
GROUP BY department_id WITH ROLLUP;
```

> `ROLLUP` 会在结果最后加一行 `NULL` 表示总计。

---

## 7. GROUP BY + 子查询

可用于复杂统计，例如：

```sql
SELECT product_id, total
FROM (
  SELECT product_id, SUM(price * quantity) AS total
  FROM orders
  GROUP BY product_id
) AS sub
WHERE total > 10000;
```

---

## 8. 注意事项与最佳实践

### 聚合查询字段必须出现在 GROUP BY 或聚合函数中

错误示例：

```sql
SELECT name, COUNT(*) FROM employees GROUP BY department_id;
```

在这个错误的查询中：

* **`GROUP BY department_id`**: 我们要求数据库将 `employees` 表中的数据按照 `department_id` 列的值进行分组。例如，所有 `department_id` 为 101 的员工会形成一个组，所有 `department_id` 为 102 的员工会形成另一个组，以此类推。
* **`SELECT name, COUNT(*)`**: 我们要求数据库在结果中显示每个组的 `name` 和该组中员工的数量 (`COUNT(*)`）。

**问题在于：** 对于同一个 `department_id` 的分组（例如，`department_id = 101`），可能存在多个不同的 `name` 值（例如，同一个部门可能有 "Alice"、"Charlie" 等多个员工）。数据库不知道应该在结果中显示哪个员工的名字。因此，这个查询会产生歧义。

---

正确示例：

```sql
SELECT department_id, COUNT(*) FROM employees GROUP BY department_id;
```

或者启用 MySQL 8 的 `ONLY_FULL_GROUP_BY` 模式，强制严格模式。

在这个正确的查询中：

* **`GROUP BY department_id`**: 同样按 `department_id` 进行分组。
* **`SELECT department_id, COUNT(*)`**: 我们选择显示分组的依据列 `department_id` 和每个分组中的记录数 `COUNT(*)`。

这个查询是明确的，因为 `department_id` 是分组的依据，对于每个分组来说，`department_id` 的值是唯一的，所以没有歧义。


## 9. GROUP BY 性能优化

| 优化点                     | 描述                                      |
|----------------------------|-------------------------------------------|
| 索引优化                   | 优先考虑 group by 字段加索引              |
| 限制分组数量               | `LIMIT` 配合 `ORDER BY` 降低压力           |
| 子查询/临时表缓存          | 复杂分组先写入临时表再处理                 |
| 使用 `GROUP_CONCAT()` 替代 | 拼接字段时避免子查询                      |

---

## 10. 常用聚合函数

| 函数名       | 功能                  |
|--------------|-----------------------|
| `COUNT(*)`   | 行数统计              |
| `SUM(col)`   | 求和                  |
| `AVG(col)`   | 平均值                |
| `MAX(col)`   | 最大值                |
| `MIN(col)`   | 最小值                |
| `GROUP_CONCAT(col)` | 将组内数据合并为字符串 |

---


