# Mysql函数-FlowControlFunctions

MySQL 8 中的 **Flow Control Functions（流程控制函数）** 提供了条件判断、NULL处理等控制逻辑的函数，常用于 SQL 查询、存储过程、中间变量控制等。

这类函数不像聚合、日期等函数那样操作数据，而是像编程语言中的 `if-else`、`switch-case`，用于**控制结果的逻辑分支**。

---

## 1. 函数总览

| 函数名        | 说明                              |
|---------------|-----------------------------------|
| `IF()`        | 条件判断，返回两个值之一          |
| `IFNULL()`    | 判断是否为 NULL，提供默认值       |
| `NULLIF()`    | 如果两个值相等，返回 NULL         |
| `CASE`        | 类似于 switch-case，支持多个条件分支 |

---

## 2. 函数详解与示例

---

### 2.1 `IF(expr, true_value, false_value)`

类似三元运算符 `expr ? true_value : false_value`。

```sql
SELECT IF(1 < 2, 'yes', 'no');  -- yes
SELECT IF(salary > 5000, '高薪', '普通') FROM employee;
```

---

### 2.2 `IFNULL(expr1, expr2)`

如果 `expr1` 为 `NULL`，返回 `expr2`，否则返回 `expr1`。

```sql
SELECT IFNULL(NULL, '默认值');     -- 默认值
SELECT IFNULL('非空', '默认值');   -- 非空

-- 用于空字段回填默认
SELECT name, IFNULL(phone, '无电话') FROM users;
```

---

### 2.3 `NULLIF(expr1, expr2)`

如果两个表达式相等，返回 `NULL`，否则返回 `expr1`。

常用于防止除以 0，或对值判空。

```sql
SELECT NULLIF(1, 1);   -- NULL
SELECT NULLIF(2, 1);   -- 2

-- 避免除以 0
SELECT 100 / NULLIF(score, 0) FROM test_scores;
```

---

### 2.4 `CASE` 表达式（多条件判断）

**语法一（类似 switch）**：

```sql
CASE expression
  WHEN value1 THEN result1
  WHEN value2 THEN result2
  ...
  ELSE default_result
END
```

**语法二（类似 if-else if）**：

```sql
CASE
  WHEN condition1 THEN result1
  WHEN condition2 THEN result2
  ...
  ELSE default_result
END
```

**示例：**

```sql
SELECT
  name,
  CASE grade
    WHEN 'A' THEN '优秀'
    WHEN 'B' THEN '良好'
    WHEN 'C' THEN '及格'
    ELSE '不及格'
  END AS 评语
FROM student;
```

```sql
SELECT
  salary,
  CASE
    WHEN salary > 20000 THEN '高薪'
    WHEN salary > 10000 THEN '中薪'
    ELSE '低薪'
  END AS 薪资等级
FROM employee;
```

---

