# Mysql函数-CompressionFunctions

在 MySQL 8 中，**Compression Functions（压缩函数）** 提供了对字符串数据的压缩与解压功能，常用于减少存储空间或传输时的数据量。


---

## 1. 压缩函数

| 函数名              | 说明                         |
|---------------------|------------------------------|
| `COMPRESS(str)`     | 使用 zlib 压缩字符串         |
| `UNCOMPRESS(str)`   | 解压 `COMPRESS()` 得到的数据 |
| `UNCOMPRESS_LENGTH(str)` | 返回解压后字符串长度     |

所有这些函数都使用 **zlib** 压缩算法，适合压缩文本或结构性数据。

---

## 2. COMPRESS(str)

### 功能：
将输入字符串使用 `zlib` 压缩，并返回 **二进制字符串**。

### 语法：
```sql
COMPRESS(str)
```

### 示例：
```sql
SELECT COMPRESS('Hello, MySQL!');
```

返回值是一个二进制 blob，不是可读字符串。

---

## 3. UNCOMPRESS(str)

### 功能：
将 `COMPRESS()` 得到的值进行解压，返回原始字符串。

### 语法：
```sql
UNCOMPRESS(str)
```

### 示例：
```sql
SELECT UNCOMPRESS(COMPRESS('Hello, MySQL!'));
-- 返回原文 'Hello, MySQL!'
```

### 注意：
- 如果输入不是 zlib 格式的压缩数据，会返回 `NULL`。
- 解压结果是字符串。

---

## 4. UNCOMPRESS_LENGTH(str)

### 功能：
返回 `UNCOMPRESS(str)` 之后结果的长度（单位：字节）。

### 语法：
```sql
UNCOMPRESS_LENGTH(str)
```

### 示例：
```sql
SELECT UNCOMPRESS_LENGTH(COMPRESS('abcdefg'));
-- 返回原始字符串的长度 7
```

这个函数适合在不解压数据的情况下，快速了解原始数据大小。

---

## 5. 应用场景

### 5.1 压缩日志或大文本
```sql
INSERT INTO logs(data_compressed)
VALUES (COMPRESS('This is a very long log content...'));
```

### 5.2 延迟解压：节省查询成本
```sql
SELECT id
FROM logs
WHERE UNCOMPRESS_LENGTH(data_compressed) > 1000;
```

---

## 6. 注意事项与限制

1. **最大压缩长度**：
    - `COMPRESS()` 只处理长度 ≤ 2^32 - 1 的字符串。

2. **结果是 BINARY 类型**：
    - `COMPRESS()` 结果是 `BLOB` 类型，不能直接显示，需配合 `HEX()`、`UNCOMPRESS()` 使用。

3. **压缩效率因内容不同而异**：
    - 重复多、结构性强的内容压缩效果好。
    - 已压缩格式（如 JPEG, ZIP 文件）压缩无意义甚至增大。

---

