# Mysql函数-Full-TextSearchFunctions

在 MySQL 8 中，**Full-Text Search Functions（全文搜索函数）** 提供了强大的自然语言搜索能力，适用于大段文本（如文章、评论、描述）中进行高效的关键词查找。和 `LIKE '%xxx%'` 不同，全文搜索基于**倒排索引（inverted index）**，性能更高，语义更强。

---

## 1. 支持的全文函数

| 函数名                         | 说明                             |
|--------------------------------|----------------------------------|
| `MATCH(col1, col2, ...) AGAINST(expr [IN MODE])` | 主函数，用于执行全文搜索 |
| `BOOLEAN MODE`                 | 布尔模式，支持 +、-、*、" " 等搜索操作符 |
| `NATURAL LANGUAGE MODE`        | 默认模式，自然语言处理 |
| `WITH QUERY EXPANSION`        | 自动扩展搜索词，提高匹配广度 |

---

## 2. 基本使用

### 2.1 建全文索引（仅支持 InnoDB + CHAR/VARCHAR/TEXT 类型）

```sql
CREATE TABLE articles (
  id INT PRIMARY KEY,
  title VARCHAR(200),
  body TEXT,
  FULLTEXT(title, body)  -- 建全文索引
);
```

### 2.2 基本搜索：`MATCH ... AGAINST`

```sql
SELECT * FROM articles
WHERE MATCH(title, body)
      AGAINST('MySQL functions');
```

默认使用 **NATURAL LANGUAGE MODE**，自动处理停用词、词干等。

---

## 3. 全文搜索模式详解

---

### 3.1 `NATURAL LANGUAGE MODE`（默认）

**特点**：

- 不支持布尔操作符。
- 按照相关性评分（relevance score）排序。
- 忽略停用词（如：a, an, the 等）。
- 自动做词干提取（如 running → run）。

```sql
SELECT id, MATCH(title, body) AGAINST('engineer jobs') AS score
FROM articles
ORDER BY score DESC;
```

---

### 3.2 `BOOLEAN MODE`

**支持高级操作符**（像 Lucene / 搜索引擎语法）：

| 操作符 | 说明 |
|--------|------|
| `+`    | 必须包含该词         |
| `-`    | 必须不包含该词       |
| `*`    | 通配符（右侧）       |
| `"`   | 短语精确匹配         |
| `>` `<` | 提升/降低词重要性  |

示例：

```sql
SELECT * FROM articles
WHERE MATCH(title, body)
      AGAINST('+mysql +index -oracle' IN BOOLEAN MODE);
```

说明：

- 包含 "mysql"
- 包含 "index"
- 不包含 "oracle"

---

### 3.3 `WITH QUERY EXPANSION`

MySQL 会基于初次查询结果中相关文档，再**扩展关键词**执行第二次查询（即伪相关反馈 pseudo-relevance feedback）。

```sql
SELECT * FROM articles
WHERE MATCH(title, body)
      AGAINST('replication' WITH QUERY EXPANSION);
```

---

## 4. 相关性分数（Relevance）

`MATCH ... AGAINST` 返回的是匹配的“**相关性分数**”，可以用于排序。

```sql
SELECT id, MATCH(title, body) AGAINST('MySQL') AS relevance
FROM articles
ORDER BY relevance DESC;
```

得分越高，说明越匹配。

---



