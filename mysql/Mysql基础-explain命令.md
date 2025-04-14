# Mysql基础-explain命令



## 1. EXPLAIN

- 是 MySQL 的 **查询优化器（Optimizer）** 提供的工具。
- 用来分析 `SELECT`、`DELETE`、`INSERT`、`REPLACE`、`UPDATE` 的执行计划（尤其以 `SELECT` 为主）。
- 通过分析其返回结果，判断是否用了索引、是否全表扫描、是否使用临时表/排序等。

---

## 2. 基本语法

```sql
EXPLAIN SELECT * FROM users WHERE id = 10;
```

MySQL 8.0 推荐使用 `EXPLAIN FORMAT=TRADITIONAL` 或 `EXPLAIN ANALYZE` 提供更详细信息。

---

## 3. EXPLAIN 各字段详解

使用 EXPLAIN 后返回一个表格，常见字段如下：

| 字段名        | 含义                                                        |
|---------------|-----------------------------------------------------------|
| `id`          | 查询中每个 `SELECT` 子句的标识，数字越大越先执行                             |
| `select_type` | 查询类型（如 SIMPLE、PRIMARY、SUBQUERY）                           |
| `table`       | 正在访问的表名或别名                                                |
| `partitions`  | 使用的分区（如果表有分区）                                             |
| `type`        | 访问类型（ALL < index < range < ref < eq_ref < const < system） |
| `possible_keys` | 可用于查询的索引                                                  |
| `key`         | 实际使用的索引                                                   |
| `key_len`     | 索引使用的长度（字节）                                               |
| `ref`         | 哪一列或常量与 key 进行比较                                          |
| `rows`        | 估算的读取行数                                                   |
| `filtered`    | 过滤后留下的行的百分比（0-100）                                        |
| `Extra`       | 额外信息，如 Using filesort / Using temporary 等                 |

---

## 4. type（访问类型）详解（越往下效率越差）

| 类型       | 含义说明 |
|------------|----------|
| `system`   | 表仅有一行，常量表 |
| `const`    | 用主键或唯一索引查找单行 |
| `eq_ref`   | 唯一索引等值连接 |
| `ref`      | 非唯一索引等值匹配 |
| `range`    | 范围扫描（比如 `BETWEEN`、`<`、`>`） |
| `index`    | 扫描整个索引 |
| `ALL`      | 全表扫描 最低效，应尽量避免 |


- **`system`**: 表中只有一行数据，这是效率最高的，因为它只需要一次查找就能找到结果。
- **`const`**: 使用主键或唯一索引查找单行，效率非常高，通常也认为是常量级的查找。
- **`eq_ref`**: 对于连接操作，使用唯一索引或主键进行等值匹配，对于左表的每一行，右表最多匹配一行，效率很高。
- **`ref`**: 使用非唯一索引进行等值匹配，可能会返回多行，效率比 `eq_ref` 稍低，但仍然是不错的访问方式。
- **`range`**: 使用索引进行范围扫描，例如 `BETWEEN`、`<`、`>` 等。效率取决于扫描的范围大小，如果范围很小，效率仍然可以接受。
- **`index`**: 扫描整个索引树来查找数据。这种方式通常比全表扫描 `ALL` 要好，因为索引一般比表小，但如果索引不能覆盖查询所需的所有列，可能还需要回表查询，效率会受到影响。
- **`ALL`**: 全表扫描，效率最低，应该尽量避免。


---

## 5. select_type（查询类型）

| 类型          | 含义 |
|---------------|------|
| `SIMPLE`      | 非嵌套查询 |
| `PRIMARY`     | 最外层 SELECT |
| `SUBQUERY`    | SELECT 出现在 WHERE 中 |
| `DERIVED`     | 派生表（FROM 子查询） |
| `UNION`       | UNION 中的第二个及其后 SELECT |
| `UNION RESULT`| UNION 的结果集合 |
| `MATERIALIZED`| 物化子查询（MySQL 8.0 新增）|

---

## 6. Extra 详解（优化关注重点）

| Extra 内容                     | 含义 |
|-------------------------------|------|
| `Using where`                 | 使用了 where 条件进行过滤 |
| `Using index`                 | 覆盖索引，无需回表 |
| `Using index condition`       | 使用了索引条件下推（ICP） |
| `Using temporary`             | 用了临时表（常用于 GROUP BY） |
| `Using filesort`             | 用了文件排序  可尝试优化为索引排序 |
| `Using join buffer`          | 没用上索引进行连接，需关注️ |
| `Impossible WHERE`           | where 永远不成立 |
| `Select tables optimized away`| 优化器直接算出结果 |

---


## 7. EXPLAIN ANALYZE（MySQL 8.0+）

```sql
EXPLAIN ANALYZE SELECT * FROM user WHERE age = 30;
```

- 会**真实执行**语句并输出执行耗时、每步处理行数、是否回表等信息。
- 是 **实际性能调优最有用的工具之一**。

---

## 8. EXPLAIN FORMAT=JSON（更详细的结构化信息）

```sql
EXPLAIN FORMAT=JSON SELECT * FROM user WHERE age = 30;
```

- 会返回一个 JSON 字符串，其中包含执行计划的所有细节，适合配合程序做自动分析。

---

## 9. 优化建议小结

1. 尽量避免 `type = ALL`（全表扫描）。
2. `Extra` 中避免 `Using filesort`、`Using temporary`。
3. 索引是否被使用，看 `key` 字段。
4. `rows` 越小，执行效率越高。
5. 使用 `EXPLAIN ANALYZE` 检查是否回表（Extra 中是否出现 `Using index`）。

---
