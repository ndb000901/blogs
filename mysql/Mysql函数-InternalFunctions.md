# Mysql函数-InternalFunctions

在 MySQL 8 中，`Internal Functions`（内部函数）不是面向用户的标准 SQL 函数，而是指 MySQL **内部使用的特殊函数或系统函数**。这些函数 **通常不会出现在普通 SQL 查询中**，而是由 MySQL 优化器、执行器、复制逻辑等内部组件使用，有些在执行计划或错误信息中偶尔可见。


---

## 1. 常见的 Internal Function 类型

### 1.1 `INTERNAL_TMP_TABLE`
> 表示内部使用的 **临时表操作**。

在执行复杂 `GROUP BY`、`ORDER BY`、`DISTINCT`、子查询时，MySQL 可能会使用内部临时表结构。

你可通过 `EXPLAIN` 看出是否创建了 Internal Temporary Table：

```sql
EXPLAIN SELECT DISTINCT name FROM users ORDER BY created_at;
```

```text
Extra: Using temporary; Using filesort
```

内部函数如：
- `_utf8mb4_general_ci`：用于排序、字符集比较。
- `tmp_table`：代表临时表相关操作。

---

### 1.2 `_latin1`, `_utf8mb4`, `_binary` 等前缀函数
这是 MySQL 内部对字符串进行字符集强制转换的方式。

```sql
SELECT _utf8mb4'你好';  -- 强制字符串为 utf8mb4 编码
```

这些是 **内部函数**，常被优化器和 COLLATION 引擎隐式调用。

---

### 1.3 隐式的 `VALUES()` 函数
在执行 `INSERT ... ON DUPLICATE KEY UPDATE` 时，`VALUES(col)` 实际是一个内部函数：

```sql
INSERT INTO user(id, name) VALUES(1, 'Tom')
ON DUPLICATE KEY UPDATE name = VALUES(name);
```

MySQL 8.0 中推荐用 `NEW.name` 代替该内部函数（已废弃）。

---

### 1.4 `WEIGHT_STRING()`
> 内部排序函数，按照排序规则返回字符串的权重值。

```sql
SELECT WEIGHT_STRING('abc');
```

通常不建议直接使用，仅在调试排序规则或 COLLATION 问题时使用。

---

### 1.5 `INTERNAL_FUNCTION_NAME()` 出现在执行计划或错误日志中
有些复杂 SQL 使用 `EXPLAIN FORMAT=JSON`，会显示内部优化函数调用名，如：

```sql
EXPLAIN FORMAT=JSON SELECT * FROM t1 WHERE t1.a = t1.b;
```

结果中可能会看到：
```json
"attached_condition": "internal_tmp_table"
```

---

### 1.6 系统内部表达式，如：
- `SYS_OP_BITVEC`、`SYS_OP_MAP_NONNULL`（仅见于 trace log 或执行计划）
- `JSON_TABLE` 内部生成的 column path expression
- `ROWID()`（在一些存储引擎内部使用）

这些函数不是公开文档的一部分，仅限优化器/执行引擎内部逻辑使用。

---

## 2. Internal Functions 的特点

| 特征 | 说明 |
|------|------|
| 非公开 | 并非官方推荐用户直接调用 |
| 被优化器使用 | 如临时表、排序、JOIN 优化时自动使用 |
| 可在执行计划中看到 | `EXPLAIN FORMAT=JSON` 或 `performance_schema` 有时会显示 |
| 有些为调试工具提供支持 | 如 `WEIGHT_STRING()`、字符集函数 |

---

