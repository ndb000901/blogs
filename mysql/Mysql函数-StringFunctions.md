# Mysql函数-StringFunctions

MySQL 8 中的 **`String Functions`**（字符串函数）是处理和操作字符串数据的函数集合。这些函数涵盖了字符串的拼接、查找、替换、截取、格式化等操作，对于文本数据处理和查询非常有用。

---

## 1. 字符串函数分类

| 类别 | 示例函数 | 说明 |
|------|----------|------|
| 字符串查找与替换 | `CONCAT()`, `INSTR()`, `REPLACE()` | 字符串拼接、查找、替换 |
| 子字符串操作 | `SUBSTRING()`, `LEFT()`, `RIGHT()` | 字符串截取 |
| 字符串长度与空白处理 | `LENGTH()`, `TRIM()`, `CHAR_LENGTH()` | 获取长度、去除空白 |
| 字符串转换与格式化 | `UPPER()`, `LOWER()`, `FORMAT()` | 大小写转换、格式化 |
| 字符串比较 | `LIKE`, `REGEXP` | 字符串匹配、正则表达式匹配 |

---

## 2. 常用函数

### 2.1 `CONCAT(str1, str2, ...)`
将多个字符串连接成一个字符串。

```sql
SELECT CONCAT('Hello', ' ', 'World!');
-- 返回 'Hello World!'
```

- 如果其中一个参数为 `NULL`，则会自动忽略 `NULL` 值。

### 2.2 `CONCAT_WS(separator, str1, str2, ...)`
通过指定的分隔符连接多个字符串。

```sql
SELECT CONCAT_WS('-', '2025', '04', '14');
-- 返回 '2025-04-14'
```

- `separator` 参数用于分隔连接的字符串。

### 2.3 `INSTR(str, substring)`
返回子字符串在字符串中首次出现的位置（从 1 开始）。

```sql
SELECT INSTR('Hello World', 'World');
-- 返回 7
```

- 如果没有找到子字符串，返回 0。

### 2.4 `SUBSTRING(str, start, length)`
从指定位置开始截取字符串，并返回指定长度的子字符串。

```sql
SELECT SUBSTRING('Hello World', 7, 5);
-- 返回 'World'
```

- `start` 是起始位置，`length` 是截取的长度。

### 2.5 `LEFT(str, length)`
从字符串的左侧开始截取指定长度的子字符串。

```sql
SELECT LEFT('Hello World', 5);
-- 返回 'Hello'
```

### 2.6 `RIGHT(str, length)`
从字符串的右侧开始截取指定长度的子字符串。

```sql
SELECT RIGHT('Hello World', 5);
-- 返回 'World'
```

### 2.7 `REPLACE(str, from_str, to_str)`
替换字符串中的指定部分。

```sql
SELECT REPLACE('Hello World', 'World', 'MySQL');
-- 返回 'Hello MySQL'
```

- `from_str` 是要替换的子字符串，`to_str` 是替换后的字符串。

### 2.8 `TRIM([remstr] FROM str)`
去掉字符串两端的空白字符或指定字符。

```sql
SELECT TRIM('  Hello World  ');
-- 返回 'Hello World'
```

- 可以通过指定 `remstr` 参数来去除指定字符。

### 2.9 `LENGTH(str)`
返回字符串的字节长度（每个字符的字节数）。

```sql
SELECT LENGTH('Hello World');
-- 返回 11
```

- 对于多字节字符集（如 UTF-8），每个字符的字节数可能大于 1。

### 2.10 `CHAR_LENGTH(str)` 或 `CHARACTER_LENGTH(str)`
返回字符串的字符长度（字符数，而非字节数）。

```sql
SELECT CHAR_LENGTH('Hello World');
-- 返回 11
```

- 适用于多字节字符集，按字符计数，而非字节。

### 2.11 `UPPER(str)`
将字符串中的所有字母转换为大写字母。

```sql
SELECT UPPER('Hello World');
-- 返回 'HELLO WORLD'
```

### 2.12 `LOWER(str)`
将字符串中的所有字母转换为小写字母。

```sql
SELECT LOWER('Hello World');
-- 返回 'hello world'
```

### 2.13 `FORMAT(X, D)`
格式化数字为字符串，保留指定的小数位数。

```sql
SELECT FORMAT(12345.6789, 2);
-- 返回 '12,345.68'
```

- `D` 是保留的小数位数。

### 2.14 `TRIM(LEADING 'char' FROM str)` / `TRIM(TRAILING 'char' FROM str)` / `TRIM(BOTH 'char' FROM str)`
去掉字符串的前导、尾部或两端的指定字符。

```sql
SELECT TRIM(LEADING 'x' FROM 'xxxHello Worldxxx');
-- 返回 'Hello Worldxxx'

SELECT TRIM(TRAILING 'x' FROM 'xxxHello Worldxxx');
-- 返回 'xxxHello World'

SELECT TRIM(BOTH 'x' FROM 'xxxHello Worldxxx');
-- 返回 'Hello World'
```

---

## 3. 字符串比较与匹配

### 3.1 `LIKE`
用于模式匹配，支持通配符。

```sql
SELECT * FROM users WHERE name LIKE 'J%';
-- 查找所有名字以 'J' 开头的用户
```

- `%` 表示任意数量的字符，`_` 表示一个字符。

### 3.2 `REGEXP`（正则表达式）
使用正则表达式进行字符串匹配。

```sql
SELECT * FROM users WHERE name REGEXP '^J';
-- 查找所有名字以 'J' 开头的用户
```

- 支持更复杂的模式匹配。

---

