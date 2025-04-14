# Mysql函数-JSONFunctions

MySQL 8 对 JSON 提供了强大而丰富的支持，内置了大量 **JSON 函数**，用于创建、查询、修改、合并、验证等操作，统称为 **JSON Functions**。

---

## 1. JSON 类型支持简述

MySQL 8 引入了原生的 `JSON` 数据类型，具有如下优势：

- 存储为二进制格式（紧凑、高效）
- 支持索引（部分索引或虚拟列 + 索引）
- 支持丰富的 JSON 内置函数

---

## 2. JSON 函数总览（按功能分类）

| 分类 | 常用函数 |
|------|----------|
| 创建 JSON | `JSON_OBJECT()`、`JSON_ARRAY()`、`JSON_QUOTE()` |
| 查询 JSON | `JSON_EXTRACT()`、`->`、`->>`、`JSON_UNQUOTE()` |
| 修改 JSON | `JSON_SET()`、`JSON_REPLACE()`、`JSON_REMOVE()` |
| 合并 JSON | `JSON_MERGE_PRESERVE()`、`JSON_MERGE_PATCH()` |
| 路径处理 | `JSON_CONTAINS_PATH()`、`JSON_SEARCH()` |
| 类型/格式判断 | `JSON_TYPE()`、`JSON_VALID()` |
| 表达式转换 | `JSON_TABLE()`（非常强大） |

---

## 3. 函数示例

---

### 3.1 `JSON_OBJECT(key1, val1, key2, val2, ...)`
构造 JSON 对象。

```sql
SELECT JSON_OBJECT('name', 'Tom', 'age', 30);
-- {"name": "Tom", "age": 30}
```

---

### 3.2 `JSON_ARRAY(val1, val2, ...)`
构造 JSON 数组。

```sql
SELECT JSON_ARRAY('apple', 123, true);
-- ["apple", 123, true]
```

---

### 3.3 `JSON_QUOTE(str)`
将字符串转为合法的 JSON 字符串（加引号、转义）。

```sql
SELECT JSON_QUOTE('Tom "Hanks"');
-- "Tom \"Hanks\""
```

---

### 3.4 `JSON_EXTRACT(json_doc, path)` 或 `->`
提取 JSON 字段。

```sql
SELECT JSON_EXTRACT('{"name":"Tom","age":30}', '$.name');
-- "Tom"

-- 简写形式
SELECT '{"name":"Tom"}' -> '$.name';   -- "Tom"
SELECT '{"name":"Tom"}' ->> '$.name';  -- Tom（去引号）
```

---

### 3.5 `JSON_UNQUOTE(json_val)`
去掉提取的 JSON 值的引号。

```sql
SELECT JSON_UNQUOTE('"Tom"');  -- Tom
```

---

### 3.6 `JSON_SET(json_doc, path, val, ...)`
修改/插入 JSON 字段（若存在则替换）。

```sql
SELECT JSON_SET('{"a":1,"b":2}', '$.b', 99, '$.c', 3);
-- {"a":1,"b":99,"c":3}
```

---

### 3.7 `JSON_REPLACE(json_doc, path, val, ...)`
只替换已存在的字段。

```sql
SELECT JSON_REPLACE('{"a":1,"b":2}', '$.b', 100, '$.c', 3);
-- {"a":1,"b":100}
```

---

### 3.8 `JSON_REMOVE(json_doc, path, ...)`
删除 JSON 中的字段。

```sql
SELECT JSON_REMOVE('{"a":1,"b":2,"c":3}', '$.b');
-- {"a":1,"c":3}
```

---

### 3.9 `JSON_CONTAINS(doc, val, path)`
检查是否包含指定值。

```sql
SELECT JSON_CONTAINS('["a", "b", "c"]', '"b"');
-- 1
```

---

### 3.10 `JSON_CONTAINS_PATH(doc, 'one'|'all', path1, path2, ...)`
检查一个或多个路径是否存在。

```sql
SELECT JSON_CONTAINS_PATH('{"a":1,"b":2}', 'all', '$.a', '$.b');  -- 1
```

---

### 3.11 `JSON_SEARCH(json_doc, 'one'|'all', search_str)`
在 JSON 文档中搜索值，返回匹配路径。

```sql
SELECT JSON_SEARCH('{"name":"Tom","city":"Tokyo"}', 'one', 'Tokyo');
-- "$.city"
```

---

### 3.12 `JSON_TYPE(json_val)`
返回 JSON 值的类型。

```sql
SELECT JSON_TYPE('[1,2,3]');  -- ARRAY
```

---

### 3.13 `JSON_VALID(str)`
验证是否是合法 JSON。

```sql
SELECT JSON_VALID('{"a": 1}');  -- 1
SELECT JSON_VALID('{a: 1}');    -- 0
```

---

### 3.14 `JSON_MERGE_PATCH()` 和 `JSON_MERGE_PRESERVE()`

```sql
-- PATCH：后者覆盖前者相同 key
SELECT JSON_MERGE_PATCH('{"a":1}', '{"a":2,"b":3}');
-- {"a":2,"b":3}

-- PRESERVE：相同 key 会合并成数组
SELECT JSON_MERGE_PRESERVE('{"a":1}', '{"a":2,"b":3}');
-- {"a": [1, 2], "b": 3}
```

---

### 3.15 `JSON_TABLE()`（MySQL 8 新增）
将 JSON 文档“虚拟”成一张表，适合与 `JOIN`、`WHERE` 联用，强大实用。

```sql
SELECT * FROM JSON_TABLE(
  '[{"id":1,"name":"Tom"},{"id":2,"name":"Jerry"}]',
  '$[*]' COLUMNS (
    id INT PATH '$.id',
    name VARCHAR(100) PATH '$.name'
  )
) AS jt;
```

输出：

| id | name  |
|----|-------|
| 1  | Tom   |
| 2  | Jerry |

---

## 4. 注意事项

- JSON 路径表达式必须以 `$` 开头，如 `$.a.b[0]`
- JSON 类型字段不能直接使用 `=` 比较，要用 `JSON_EXTRACT()` + `CAST()` 或 `JSON_UNQUOTE()` 转换
- 可以结合虚拟列、`INDEX` 优化 JSON 查询
- `JSON_TABLE()` 是复杂 JSON 查询首选，性能好、结构清晰

---


