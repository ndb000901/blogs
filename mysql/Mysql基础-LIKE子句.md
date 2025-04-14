# Mysql基础-LIKE子句

## 1. `LIKE` 的作用

`LIKE` 用于在 `WHERE` 子句中进行 **模糊匹配**，常用于字符串字段的搜索。

```sql
SELECT * FROM users WHERE name LIKE 'J%';
```

---

## 2. 语法结构

```sql
SELECT column_list
FROM table_name
WHERE column_name LIKE pattern;
```

---

## 3. 通配符说明

| 通配符 | 含义                  | 示例                   |
|--------|-----------------------|------------------------|
| `%`    | 匹配任意长度任意字符   | `'a%'` 匹配以 a 开头的 |
| `_`    | 匹配任意单个字符       | `'a_c'` 匹配 abc、a1c  |

---

### 常见匹配模式

| 模式            | 匹配效果                           | 示例                                         |
|------------------|------------------------------------|----------------------------------------------|
| `'abc%'`         | 以 abc 开头                        | `name LIKE 'abc%'` 匹配 abc, abcde, abc123   |
| `'%xyz'`         | 以 xyz 结尾                        | `name LIKE '%xyz'` 匹配 abcxyz, 123xyz       |
| `'%abc%'`        | 含有 abc                           | `name LIKE '%abc%'` 匹配 testabcxyz          |
| `'a_c'`          | 匹配 a 和 c 中间任意一个字符       | `name LIKE 'a_c'` 匹配 abc, a1c, axc          |
| `'a__c'`         | 中间任意两个字符                   | `name LIKE 'a__c'` 匹配 abxc, axxc 等         |

---

## 4. 大小写敏感性

- **默认行为** 取决于字段的字符集和排序规则（collation）。
- `utf8mb4_general_ci` 是 **大小写不敏感**。
- `utf8mb4_bin` 是 **大小写敏感**。

```sql
-- 大小写不敏感（默认）
SELECT * FROM users WHERE name LIKE 'john';

-- 强制大小写敏感（使用 BINARY 关键字）
SELECT * FROM users WHERE BINARY name LIKE 'john';
```

---

## 5. 与 NOT LIKE 搭配

```sql
-- 不匹配 abc 开头
SELECT * FROM table WHERE name NOT LIKE 'abc%';
```

---

## 6. LIKE 与 ESCAPE 转义字符

如果要匹配 `%` 或 `_` 字符本身，可以使用 `ESCAPE`。

```sql
-- 匹配 name 中含有 "100%" 字符串
SELECT * FROM products
WHERE description LIKE '%100\%%' ESCAPE '\';
```

---

## 7. LIKE 与中文、emoji 字符

- 对 `utf8mb4` 编码的字段，`LIKE '%表情%'` 可以正常匹配 emoji。
- 但性能较差，建议结合全文索引或正则替代。

---

## 8. LIKE 与索引性能问题

### 索引能生效的情况：

```sql
WHERE name LIKE 'abc%'  -- 索引可用（前缀匹配）
```

### 索引失效的情况：

```sql
WHERE name LIKE '%abc'   -- 后缀或模糊匹配无法使用索引
WHERE name LIKE '%abc%'  -- 全模糊
```

### 优化建议：

- 使用 **前缀匹配** 替代 `%xxx%`。
- 对大文本建议用 **全文索引** (`FULLTEXT`) 或 `IN BOOLEAN MODE`。
- 若数据量大，可考虑专门的全文搜索引擎（如 Elasticsearch）。



