# Mysql基础-UNION

## 1. UNION

`UNION` 用于将两个或多个 `SELECT` 查询的结果 **合并成一个结果集**，要求每个 `SELECT` 的字段数量和类型必须一致。

---

## 2. 基本语法

```sql
SELECT column1, column2 FROM table1
UNION
SELECT column1, column2 FROM table2;
```

### 特点：
- 自动去重（只保留唯一行）
- 默认排序为第一个 SELECT 的顺序

---

## 3. UNION ALL vs UNION

| 用法         | 功能                    | 是否去重 |
|--------------|-------------------------|--------|
| `UNION`      | 合并结果并去除重复行     | 去重   |
| `UNION ALL`  | 合并所有结果（包括重复） | 不去重，性能更好 |

性能方面，`UNION ALL` **优于** `UNION`，因为省去了排序去重步骤。

---

## 4. 使用条件

1. 所有 `SELECT` 语句必须 **列数相同**
2. 列类型要 **兼容**
3. **ORDER BY 只能出现在最后一个 SELECT 之后**

正确示例：

```sql
SELECT id, name FROM customers
UNION
SELECT id, name FROM suppliers;
```

错误示例：

```sql
SELECT id, name FROM customers
UNION
SELECT id FROM suppliers; -- 列数不同，报错
```

---

## 5. 带 ORDER BY 的用法

必须写在整个 `UNION` 之后：

```sql
(SELECT id, name FROM a)
UNION
(SELECT id, name FROM b)
ORDER BY name;
```

或者加括号避免歧义：

```sql
SELECT id, name FROM a
UNION
SELECT id, name FROM b
ORDER BY name DESC;
```

---

## 6. UNION vs JOIN 的区别

| 对比项     | UNION                          | JOIN                               |
|------------|--------------------------------|------------------------------------|
| 合并方式   | 垂直合并（行）                   | 水平合并（列）                       |
| 目标       | 多个查询结果合并成一张表         | 多表关联组合                        |
| 是否需要关联条件 | 不需要                       | 需要 ON 条件                      |

---

## 7. UNION 的优化建议

- 使用 `UNION ALL` 替代 `UNION`（如不需去重）  
- 每个子查询加 `LIMIT` 限定记录量，提升效率  
- 避免在 `UNION` 之后直接使用子查询包裹再排序，效率差  
- 使用临时表缓存中间结果可加快查询

---


