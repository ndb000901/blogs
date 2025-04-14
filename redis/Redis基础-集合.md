# Redis基础-集合


Redis 中的集合（Set）是一种**无序、不重复**的字符串类型数据结构，适用于快速查重、标签、共同好友等场景。

---

## 1. 集合（Set）基本特性

- 每个集合可以包含多个 **唯一（不重复）字符串元素**。
- 集合中的元素是 **无序的**（与列表List不同）。
- 底层实现：
    - 元素较少时使用 **intset（整数集合）**。
    - 元素较多或包含字符串时切换为 **hashtable（字典）结构**。
- 集合支持高性能的 **并集、交集、差集** 等集合运算。

---

## 2. 常用命令

### 2.1 添加元素

```bash

SADD key member [member ...]
```

- 向集合中添加一个或多个元素。
- 返回添加成功的数量（已存在的不计入）。

```bash

SADD colors red blue green
```

---

### 2.2 删除元素

```bash

SREM key member [member ...]
```

- 移除一个或多个元素。
- 返回实际移除的元素数量。

```bash

SREM colors blue
```

---

### 2.3 随机弹出一个元素（并移除）

```bash

SPOP key [count]
```

- 随机移除一个或多个元素并返回。
- `count` 可选，表示移除的数量。

---

### 2.4 随机获取一个元素（不移除）

```bash

SRANDMEMBER key [count]
```

- 返回集合中一个或多个随机元素，不影响集合内容。
- `count > 0`：不重复；`count < 0`：可能重复。

---

### 2.5 判断成员是否存在

```bash

SISMEMBER key member
```

- 返回 1 表示存在，0 表示不存在。

---

### 2.6 获取集合长度

```bash

SCARD key
```

- 返回集合中元素数量。

---

### 2.7 获取集合所有元素

```bash

SMEMBERS key
```

- 返回集合中所有元素（无序）。

---

## 3. 集合运算命令

### 3.1 交集

```bash

SINTER key1 key2 [key3 ...]
```

- 返回所有给定集合的交集。

```bash

SINTER friends:alice friends:bob
```

---

### 3.2 并集

```bash

SUNION key1 key2 [key3 ...]
```

- 返回所有给定集合的并集。

---

### 3.3 差集

```bash

SDIFF key1 key2 [key3 ...]
```

- 返回 `key1` 的元素中不在 `key2、key3...` 中的部分。

---

### 3.4 将集合运算结果保存到新集合

```bash

SINTERSTORE dest key1 key2 ...
SUNIONSTORE dest key1 key2 ...
SDIFFSTORE dest key1 key2 ...
```

---

## 4. 集合遍历

```bash

SSCAN key cursor [MATCH pattern] [COUNT count]
```

- 游标式遍历集合（适用于大集合分页扫描）。
- 使用 `MATCH` 过滤，`COUNT` 指定每次返回元素的个数。

---



