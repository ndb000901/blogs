# Mysql基础-连接


## 1. JOIN 的作用

`JOIN` 用于将两个或多个表 **按某个关联条件连接起来**，常用于多表联合查询。

---

## 2. JOIN 的常见类型

| JOIN 类型           | 说明                                   |
|---------------------|----------------------------------------|
| `INNER JOIN`        | 返回两个表中都匹配的记录（交集）       |
| `LEFT JOIN`         | 返回左表全部 + 右表匹配（不匹配为 NULL） |
| `RIGHT JOIN`        | 返回右表全部 + 左表匹配（不匹配为 NULL） |
| `FULL JOIN`         | MySQL 不支持（可用 `UNION` 实现）       |
| `CROSS JOIN`        | 笛卡尔积，所有组合                     |
| `SELF JOIN`         | 表自己连接自己                         |

---

## 3. 基本语法结构

```sql
SELECT t1.column1, t2.column2
FROM table1 t1
JOIN table2 t2 ON t1.key = t2.key;
```

或使用明确的连接类型：

```sql
-- 内连接
SELECT * FROM a INNER JOIN b ON a.id = b.a_id;

-- 左连接
SELECT * FROM a LEFT JOIN b ON a.id = b.a_id;

-- 右连接
SELECT * FROM a RIGHT JOIN b ON a.id = b.a_id;
```

---

## 4. JOIN 类型详解

### 4.1 INNER JOIN（内连接）

返回两个表中匹配的行。

```sql
SELECT u.name, o.amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

> 等价于 `JOIN`，常用于获取有关联的数据。

---

### 4.2 LEFT JOIN（左连接）

返回左表所有记录，右表没有匹配则填 NULL。

```sql
SELECT u.name, o.amount
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

> 用于找出“用户即使没有订单也要显示”的场景。

---

### 4.3 RIGHT JOIN（右连接）

返回右表所有记录，左表没有匹配则填 NULL。

```sql
SELECT u.name, o.amount
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

---

### 4.4 CROSS JOIN（笛卡尔积）

生成所有组合，无关联条件时慎用！

```sql
SELECT * FROM colors CROSS JOIN sizes;
-- 等价于：SELECT * FROM colors, sizes;
```

---

### 4.5 SELF JOIN（自连接）

一个表连接自己，通常需要别名。

```sql
SELECT a.name AS employee, b.name AS manager
FROM employees a
JOIN employees b ON a.manager_id = b.id;
```

---

## 5. FULL JOIN 的模拟方式

它将左表和右表中的所有行都包含在结果集中：

- 如果左表中的某行在右表中找到匹配的行（基于 ON 子句指定的连接条件），则结果集中会包含这两行的列。
- 如果左表中的某行在右表中没有匹配的行，则结果集中该行的右表列部分将填充 NULL 值。
- 如果右表中的某行在左表中没有匹配的行，则结果集中该行的左表列部分将填充 NULL 值。

简单来说，FULL JOIN 返回左表的所有行、右表的所有行，以及它们之间匹配的行。

MySQL 不支持 `FULL OUTER JOIN`，可用 `UNION` 模拟：

```sql
SELECT * FROM A LEFT JOIN B ON A.id = B.id
UNION
SELECT * FROM A RIGHT JOIN B ON A.id = B.id;
```

---

## 6. JOIN 多个表

```sql
SELECT *
FROM users u
JOIN orders o ON u.id = o.user_id
JOIN payments p ON o.id = p.order_id;
```

---

## 7. JOIN 与 WHERE 区别

```sql
-- JOIN 条件写在 WHERE 中（旧写法）
SELECT * FROM a, b WHERE a.id = b.a_id;

-- 推荐用 JOIN ON，语义更清晰
SELECT * FROM a JOIN b ON a.id = b.a_id;
```

---

## 8. JOIN 性能优化建议

- **确保连接字段上有索引**  
- 优先使用 `INNER JOIN`（更高效）  
- 避免 `SELECT *`，只取必要字段  
- 对大表连接时考虑临时表或分页  
- 慎用 `LEFT JOIN` + `WHERE xxx IS NULL`（容易慢）

---


