# Mysql基础-视图

## 1. 视图（View）

**视图**是一个虚拟表（virtual table），它的内容由 SQL 查询定义。  
视图不存储数据，仅保存查询逻辑，查询视图时会动态执行它所代表的 SQL 语句。


| 优点                                | 说明 |
|-------------------------------------|------|
| 简化复杂查询                        | 将多表复杂联表封装成一个视图 |
| 提供数据安全                        | 只暴露部分字段/数据给用户 |
| 实现逻辑封装和模块化               | SQL 逻辑集中维护 |
| 提高代码复用                        | 一处定义，多处使用 |

---

## 2. 创建视图

```sql
CREATE [OR REPLACE] VIEW view_name AS
SELECT columns
FROM table
[WHERE condition];
```

### 示例：

```sql
CREATE VIEW active_users AS
SELECT id, name, last_login
FROM users
WHERE status = 'active';
```

### `OR REPLACE`：若视图已存在则替换

---

## 3. 查看与使用视图

### 查看视图：

```sql
SELECT * FROM active_users;
```

### 查看定义：

```sql
SHOW CREATE VIEW active_users;
```

---

## 4. 更新视图

### 替换已有视图：

```sql
CREATE OR REPLACE VIEW active_users AS
SELECT id, name FROM users WHERE is_active = 1;
```

### 修改视图的算法、定义者等：

```sql
ALTER VIEW active_users AS
SELECT id, name, email FROM users WHERE status = 'active';
```

---

## 5. 删除视图

```sql
DROP VIEW IF EXISTS view_name;
```

---

## 6. 带参数的模拟（视图不能接收参数！）

虽然视图不能像存储过程那样接受参数，但可以结合 **WHERE + 查询条件** 实现效果：

```sql
SELECT * FROM active_users WHERE last_login >= '2024-01-01';
```

---

## 7. 可更新视图（Updatable Views）

视图**可以更新数据**，但必须满足以下条件：

**基本条件：**

* **单表视图：** 视图必须直接或间接地引用**一个**基表。如果视图基于多个表的连接、聚合或其他复杂操作，通常是不可更新的。

**详细条件：**

* **视图中的列与基表的列之间存在明确的映射：** 视图中的每一列都必须能够明确地映射回基表中的一个列。这意味着：
    * **没有使用聚合函数：** 视图的 `SELECT` 列表中不能包含聚合函数（如 `SUM()`、`AVG()`、`COUNT()`、`MIN()`、`MAX()` 等），因为这些函数的结果是基于多行计算的，无法直接映射回单个基表的行。
    * **没有使用 `GROUP BY` 或 `HAVING` 子句：** 这些子句用于分组和过滤数据，导致视图中的行可能代表基表中多行的聚合结果，因此不可直接更新。
    * **没有使用 `DISTINCT` 关键字：** `DISTINCT` 用于去除重复行，更新视图中的一行可能会导致重复行的出现，从而产生不确定性。
    * **没有使用复杂的表达式或计算列：** 视图 `SELECT` 列表中派生出的列（例如 `column1 + column2`，`UPPER(column3)`）通常是不可更新的，因为 MySQL 无法确定如何将更新后的值反向应用到基表的列。但如果表达式非常简单，直接对应基表的一列，则可能可以更新。
    * **没有使用子查询在 `FROM` 子句中引入多个逻辑表：** 虽然某些简单的带有子查询的视图在较新的 MySQL 版本中可能可以更新，但通常来说，如果 `FROM` 子句中包含多个逻辑表（即使是通过子查询），视图通常是不可更新的。

* **视图包含基表的所有 `NOT NULL` 列（对于 `INSERT` 操作）：** 如果您想要通过视图插入新的数据行，视图的 `SELECT` 列表必须包含基表中所有定义为 `NOT NULL` 且没有默认值的列。否则，在插入时会因为缺少必要的值而失败。

* **视图定义中没有使用 `ALGORITHM = TEMPTABLE`：** 如果创建视图时指定了 `ALGORITHM = TEMPTABLE`，那么视图的结果通常会存储在临时表中，对临时表的修改不会直接反映到基表中，因此这种视图一般是不可更新的。默认情况下，MySQL 会尝试使用 `ALGORITHM = MERGE`，这种算法通常更适合创建可更新的视图。

* **基表本身是可更新的：** 视图的基表必须是可更新的。例如，如果基表是一个只读表或者是一个不允许修改的外部数据源，那么基于该基表的视图也是不可更新的。


### 示例（可更新）：

```sql
CREATE VIEW user_basic AS
SELECT id, name, email FROM users;
```

```sql
UPDATE user_basic SET name = 'Tom' WHERE id = 1;
```

---

## 8. WITH CHECK OPTION

控制视图更新时的数据是否必须满足视图的 WHERE 条件。

```sql
CREATE VIEW active_users AS
SELECT * FROM users WHERE status = 'active'
WITH CHECK OPTION;
```

> 尝试通过该视图插入 `status != 'active'` 的数据将失败。

---

## 9. INFORMATION_SCHEMA 视图信息查询

```sql
SELECT * FROM INFORMATION_SCHEMA.VIEWS
WHERE TABLE_SCHEMA = 'your_db_name';
```

---

## 10. 视图 vs 表

| 特性         | 表（Table）    | 视图（View）         |
|--------------|----------------|-----------------------|
| 数据存储     | 存储数据        | 不存数据（虚拟）      |
| 可更新       | 可更新         | 受限制（见上）       |
| 权限控制     | 支持            | 支持（可细粒度控制字段）  |
| 性能         | 更好            | 依赖底层查询执行效率    |
| 用途         | 持久化数据存储     | 封装查询逻辑、简化权限   |

---

## 11. 视图使用建议

- 用于**简化复杂 SQL**  
- 用于**数据隔离和权限控制**  
- 在 BI 报表、API 查询中做“逻辑表”封装  
- 慎用于高频率的复杂联表查询

---

