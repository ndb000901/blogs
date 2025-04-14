# Redis基础-哈希


## 1. Redis Hash

Redis 的 Hash（哈希）是一个 **键值对集合（field-value）**，可以理解为一个小型的 **字符串字典 / Map**。它允许你在一个 key 下保存多个字段（field）及其对应的值（value）。

### 示例：

```bash

HMSET user:1000 name "Tom" age "25" gender "male"
```

---

## 2. 底层实现

Redis Hash 的底层实现根据字段数量和 value 长度自动选择：

- **ziplist（压缩列表）**：小数据（字段数 < 512 且每个字符串 < 64 字节）。
- **hashtable（哈希表）**：数据量或字段过大时自动升级。

可以通过配置文件调整这两个阈值：

```ini
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
```

---

## 3. 常用命令

### 3.1 添加/修改字段

```bash

HSET key field value
```

- 如果 `field` 不存在，则添加；
- 如果存在，则更新。

> 返回 1 表示新增字段，返回 0 表示更新字段。

```bash

HSET user:1 name Tom
```

---

### 3.2 判断字段是否存在

```bash

HEXISTS key field
```

返回 1 表示存在，0 表示不存在。

---

### 3.3 删除字段

```bash

HDEL key field [field ...]
```

支持删除多个字段。

```bash

HDEL user:1 age gender
```

---

### 3.4 获取字段值

```bash

HGET key field
```

---

### 3.5 获取所有字段和值

```bash

HGETALL key
```

返回一个扁平的列表：[field1, value1, field2, value2, …]

---

### 3.6 获取所有字段名

```bash

HKEYS key
```

---

### 3.7 获取所有字段值

```bash

HVALS key
```

---

### 3.8 获取字段数量

```bash

HLEN key
```

---

### 3.9 批量操作

```bash

HMSET key field1 value1 field2 value2 ...
HMGET key field1 field2 ...
```

- `HMSET` 是批量设置多个字段（Redis 4.0 起被标记为 deprecated，但仍可用）
- `HMGET` 是批量获取多个字段值

---

### 3.10 数值自增/自减

```bash

HINCRBY key field increment
HINCRBYFLOAT key field increment
```

- 若字段不存在则创建，默认初始值为 0。
- 支持整数和浮点数。

---

### 3.11 遍历字段

```bash

HSCAN key cursor [MATCH pattern] [COUNT count]
```

- 用于遍历大哈希表。
- 类似于 `SCAN` 命令。

---



## 4. 注意事项

- 一个 hash key 最多能存 2^32 - 1 ≈ 40 亿个 field。
- 大量字段时避免一次性使用 `HGETALL`（可能导致阻塞）。
- 对于大型哈希，推荐用 `HSCAN` 分批遍历。
- 和关系型数据库不同，Redis 不支持条件查询、排序等功能。

---


