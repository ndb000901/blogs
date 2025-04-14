# Mysql函数-CastFunctions

MySQL 8 中的 `CAST` 和 `CONVERT` 函数属于 **Cast Functions（类型转换函数）**，是日常开发、数据清洗、字段转换中极其重要的工具。

---

## 1. Cast Functions 概述

### 常见函数有：
- `CAST(expr AS type)`
- `CONVERT(expr, type)` （功能上与 `CAST` 相同）
- `BINARY`、`CHAR()`、`DATE()`、`TIME()`、`DECIMAL()` 等也算 Cast 函数家族一员。

这些函数主要用于：
- 明确告诉 MySQL：要把某个值转换为某种 **数据类型**（字符串、整数、浮点、小数、日期、时间、JSON、BINARY 等）。

---

## 2. CAST(expr AS type)

标准 SQL 写法，推荐。

### 支持的数据类型有：
| 类型       | 说明                                |
|------------|-------------------------------------|
| `CHAR`     | 转换为字符串                         |
| `BINARY`   | 转为二进制字符串（Binary string）    |
| `DATE`     | 转为 `YYYY-MM-DD` 格式               |
| `DATETIME` | 转为 `YYYY-MM-DD HH:MM:SS` 格式      |
| `TIME`     | 转为 `HH:MM:SS` 格式                 |
| `SIGNED`   | 转为带符号整数                       |
| `UNSIGNED` | 转为无符号整数                       |
| `DECIMAL[(M,D)]` | 转为高精度小数                |
| `JSON`     | 转为 JSON 类型（MySQL 5.7+ 支持）     |

---

### 示例：

#### 字符串转整数：
```sql
SELECT CAST('123' AS UNSIGNED);  -- 123
```

#### 字符串转时间：
```sql
SELECT CAST('2024-01-01 13:00:00' AS DATETIME);
```

#### 浮点数转整数（取整）：
```sql
SELECT CAST(3.1415 AS SIGNED);   -- 3
```

#### 转换为二进制（区分大小写、排序）：
```sql
SELECT CAST('abc' AS BINARY);    -- 'abc'，但是 binary 格式
```

#### 转换为 JSON：
```sql
SELECT CAST('{"a":1}' AS JSON);
```

---

## 3. CONVERT(expr, type)

`CONVERT()` 函数是 `CAST()` 的别名，语法稍有不同，但作用完全一样：

### 示例：
```sql
SELECT CONVERT('123' USING utf8mb4);  -- 字符集转换
SELECT CONVERT('123' , UNSIGNED);     -- 类型转换
```

> `CONVERT()` 既可以用于 **类型转换**，也能用于 **字符集转换**，区别在于 `USING` 关键字是否存在。

---

## 4. 其他常用 Cast 相关函数

### 4.1 `BINARY expr`
将一个表达式转换为二进制字符串（区别大小写、影响排序）

```sql
SELECT 'abc' = 'ABC';           -- 1（不区分大小写）
SELECT BINARY 'abc' = 'ABC';    -- 0（区分大小写）
```

---

### 4.2 `CHAR(N, USING charset_name)`
将数字编码为字符

```sql
SELECT CHAR(65);                      -- 'A'
SELECT CHAR(0xE4, 0xB8, 0xAD USING utf8mb4); -- '中'
```

---

### 4.3 `DECIMAL(M,D)` 精度转换

```sql
SELECT CAST(123.456 AS DECIMAL(6,2)); -- 123.46（四舍五入）
```

---

### 4.4 `JSON_TYPE() + CAST TO JSON`

```sql
SELECT JSON_TYPE(CAST('{"a":1}' AS JSON));  -- OBJECT
```

---

## 5. 类型转换规则与注意事项

### 5.1 自动 vs 显式转换
MySQL 在某些场景会自动转换类型，但这有时候不安全，建议显式使用 `CAST()`。

### 5.2 隐式转换容易出错：

```sql
SELECT 1 + '2a';    -- 结果是 1（'2a' 转为 0）
```

不如这样写更安全：

```sql
SELECT 1 + CAST('2a' AS UNSIGNED); -- 结果是 1（'2a' → 0）
```

---

### 5.3 CAST 会截断、四舍五入或出错

```sql
SELECT CAST('2024-04-01x' AS DATE); -- '2024-04-01'
SELECT CAST('abc' AS UNSIGNED);    -- 0
```

---


