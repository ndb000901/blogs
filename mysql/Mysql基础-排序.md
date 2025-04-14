# Mysql基础-排序

## 1. `ORDER BY`

`ORDER BY` 用于**对查询结果进行排序**，默认升序（`ASC`），也可指定降序（`DESC`）。  
排序字段可以是**一列、多列或表达式**。

---

## 2. 基本语法

```sql
SELECT column1, column2
FROM table_name
ORDER BY column1 [ASC | DESC], column2 [ASC | DESC];
```

---

## 3. 示例

### 示例1：按年龄升序排列用户

```sql
SELECT name, age
FROM users
ORDER BY age ASC;
```

### 示例2：按销售额降序排列订单

```sql
SELECT order_id, total_amount
FROM orders
ORDER BY total_amount DESC;
```

### 示例3：先按部门升序，再按工资降序

```sql
SELECT department_id, salary
FROM employees
ORDER BY department_id ASC, salary DESC;
```

---

## 4. ORDER BY 支持的字段类型

你可以使用以下内容作为排序依据：

- 字段名：`ORDER BY name`
- 别名：`SELECT salary * 12 AS yearly_salary ... ORDER BY yearly_salary`
- 表达式：`ORDER BY price * quantity`
- 字段位置（不推荐）：`ORDER BY 2` 表示按第二个字段排序

---

## 5. LIMIT + ORDER BY 分页排序

分页场景常见于前端列表页：

```sql
SELECT *
FROM products
ORDER BY created_at DESC
LIMIT 10 OFFSET 20;
```

等价于：

```sql
LIMIT 20, 10;  -- 从第21条开始，取10条
```

---

## 6. ORDER BY + NULL 处理

### NULL 默认排序

- 升序（ASC）：`NULL` 最前
- 降序（DESC）：`NULL` 最后

### 控制 NULL 位置（MySQL 8.0 支持）

```sql
ORDER BY column_name ASC NULLS LAST;
ORDER BY column_name DESC NULLS FIRST;
```

---

## 7. ORDER BY 优化建议

| 优化策略                 | 描述                                                       |
|--------------------------|------------------------------------------------------------|
| 建立合适索引             | 排序字段建索引可避免临时文件排序                           |
| 减少排序字段数量         | 只保留必要字段，减少内存开销                               |
| 避免 filesort            | 优化查询使其使用索引排序而非文件排序                      |
| 配合 LIMIT 使用          | 减少排序结果数量                                           |
| ORDER BY 和 WHERE 一起优化 | 先过滤再排序，避免排序大数据集                            |

查看排序方式：

```sql
EXPLAIN SELECT ... ORDER BY ...;
```

观察是否出现 `Using filesort`，尽量避免。

---



## 8. ORDER BY + CASE 实现自定义排序

### 示例：自定义状态排序（未支付 > 已支付 > 已取消）

```sql
SELECT order_id, status
FROM orders
ORDER BY 
  CASE status
    WHEN 'pending' THEN 1
    WHEN 'paid' THEN 2
    WHEN 'canceled' THEN 3
    ELSE 4
  END;
```

---


## 9. ORDER BY 与性能

| 查询方式           | 性能表现                   |
|--------------------|----------------------------|
| 使用索引排序       | 最佳性能                   |
| filesort 内存排序  | 次优                       |
| filesort + 磁盘排序| 最慢，需优化               |

使用 `EXPLAIN` 分析是否命中索引、是否触发临时排序。

---
