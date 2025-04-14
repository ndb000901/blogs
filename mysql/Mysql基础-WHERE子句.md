# Mysql基础-WHERE子句

## 1. `WHERE` 语句作用

用于 **筛选查询数据**，仅返回满足条件的行，常见于 `SELECT`、`UPDATE`、`DELETE` 等语句中。

```sql
SELECT * FROM users WHERE age > 18;
```

---

## 2. 基本语法结构

```sql
SELECT column_list
FROM table_name
WHERE condition;
```

---

## 3. 常见条件运算符

### 比较运算符：

| 运算符     | 含义           | 示例                     |
|---------|----------------|--------------------------|
| =       | 等于           | `WHERE age = 20`         |
| != 或 <> | 不等于       | `WHERE status != 'ok'`   |
| \>      | 大于           | `WHERE score > 60`       |
| <       | 小于           | `WHERE price < 100`      |
| \>=     | 大于等于       | `WHERE age >= 18`        |
| <=      | 小于等于       | `WHERE age <= 65`        |

---

### 逻辑运算符：

| 运算符 | 作用                       | 示例                                               |
|--------|----------------------------|----------------------------------------------------|
| `AND`  | 与，多个条件同时满足       | `WHERE age > 18 AND gender = 'M'`                  |
| `OR`   | 或，满足任意条件即可       | `WHERE name = 'Tom' OR name = 'Jerry'`             |
| `NOT`  | 非，取反                   | `WHERE NOT (status = 'inactive')`                  |
| 括号() | 优先级控制                 | `WHERE (age > 18 AND gender = 'M') OR vip = 1`     |

---

## 4. 范围条件

### `BETWEEN` … `AND`：

```sql
-- 包含边界值
WHERE age BETWEEN 18 AND 30
```

等价于：
```sql
WHERE age >= 18 AND age <= 30
```

---

## 5. 集合判断

### `IN (...)`：

```sql
WHERE country IN ('US', 'UK', 'CA')
```

等价于：
```sql
WHERE country = 'US' OR country = 'UK' OR country = 'CA'
```

### `NOT IN (...)`：

```sql
WHERE status NOT IN ('disabled', 'banned')
```

---

## 6. 空值判断

### `IS NULL` / `IS NOT NULL`：

```sql
WHERE deleted_at IS NULL
```

```sql
WHERE email IS NOT NULL
```

---

## 7. 模糊匹配

### `LIKE` 和 `NOT LIKE`：

| 模式     | 含义           | 示例                       |
|----------|----------------|----------------------------|
| `%abc`   | 以 abc 结尾     | `WHERE name LIKE '%abc'`   |
| `abc%`   | 以 abc 开头     | `WHERE name LIKE 'abc%'`   |
| `%abc%`  | 含有 abc        | `WHERE name LIKE '%abc%'`  |
| `_`      | 匹配单个字符    | `WHERE name LIKE 'a_e'`    |

---

## 8. 正则匹配（不推荐大数据量用）

### `REGEXP`：

```sql
-- 匹配以 t 开头的用户名
WHERE username REGEXP '^t'
```

```sql
-- 匹配手机号以 13、14 或 15 开头
WHERE phone REGEXP '^1[345]'
```

---

## 9. 子查询配合 `WHERE`

```sql
SELECT name FROM users
WHERE id IN (SELECT user_id FROM orders WHERE amount > 100);
```

---

## 10. 性能优化

| 建议                              | 说明                                               |
|-----------------------------------|----------------------------------------------------|
| 使用索引字段做条件                 | 例如：`WHERE id = 1` 中 `id` 有索引                |
| 避免函数包裹列                    | `WHERE DATE(created_at) = '2024-01-01'` 不走索引   |
| 避免在索引列使用 `OR`             | 可以改为 `UNION` 查询方式                          |
| `IN` 项数量适中（<1000）           | 太多项会变慢，可以考虑临时表                      |
| 尽量避免 `SELECT *` 搭配复杂 `WHERE` | 明确字段可以加快解析与优化                         |
| 配合 `EXPLAIN` 分析执行计划       | 检查是否使用索引、是否为全表扫描                   |

---


