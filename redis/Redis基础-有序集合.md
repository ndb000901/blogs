# Redis基础-有序集合

## 1. 有序集合（Sorted Set）

Redis 的有序集合（`ZSet`）是带分数的集合：

- 每个元素是一个 **唯一的字符串成员（member）**。
- 每个成员关联一个 **浮点型的分数（score）**。
- 元素根据 score 从小到大自动排序（排序稳定）。
- 同一个集合中：member 唯一，但 score 可以重复。

---

## 2. 底层实现

Redis 有序集合底层由两个数据结构组成：

1. **跳表（skiplist）**：按 score 排序，支持快速范围查找。
2. **哈希表（dict）**：提供 O(1) 的成员查找功能。

这使得有序集合同时支持：

- 按分数查找（范围、排名）
- 按成员查找（获取 score、删除、更新）

---

## 3. 常用命令

### 3.1 添加/更新元素

```bash

ZADD key [NX|XX] [CH] [INCR] score member [score2 member2 ...]
```

- `NX`：仅添加新成员
- `XX`：仅更新已存在成员
- `CH`：返回变更元素的数量（新增或更新）
- `INCR`：将 score 累加（变成 ZINCRBY）

例：

```bash

ZADD leaderboard 100 user1 90 user2
```

---

### 3.2 累加分数（或减少）

```bash

ZINCRBY key increment member
```

- 对指定 member 的 score 加上 increment。
- increment 可为负数表示减少。

```bash

ZINCRBY leaderboard 5 user1
```

---

### 3.3 获取排名

```bash

ZRANK key member     # 正序排名（从小到大）
ZREVRANK key member  # 倒序排名（从大到小）
```

返回 member 的排名，0 是第一名，找不到返回 nil。

---

### 3.4 获取元素 score

```bash

ZSCORE key member
```

返回 member 的 score，找不到返回 nil。

---

### 3.5 获取元素总数

```bash

ZCARD key
```

返回集合中元素数量。

---

### 3.6 按 score 查询

```bash

ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]
```

- 支持范围查询，例如 `(80 100]`，使用：
    - 开区间：`(80`
    - 闭区间：`80`
    - 无穷：`-inf`、`+inf`
- `WITHSCORES`：返回值中包含 score

例：

```bash

ZRANGEBYSCORE leaderboard 90 100 WITHSCORES
```

---

### 3.7 按排名获取元素

```bash

ZRANGE key start stop [WITHSCORES]
ZREVRANGE key start stop [WITHSCORES]
```

- 类似 List 的范围下标查询，支持负数表示倒数。
- `ZREVRANGE` 返回高分到低分。

例：

```bash

ZREVRANGE leaderboard 0 9 WITHSCORES  # 前 10 名
```

---

### 3.8 删除元素

```bash

ZREM key member [member ...]
```

---

### 3.9 删除按分数范围的元素

```bash

ZREMRANGEBYSCORE key min max
```

---

### 3.10 删除按排名范围的元素

```bash

ZREMRANGEBYRANK key start stop
```

---

### 3.11 遍历有序集合

```bash

ZSCAN key cursor [MATCH pattern] [COUNT count]
```

用于大数据分页扫描。

---

## 4. 有序集合运算（合并、交集）

### 4.1 并集 / 交集计算并存储到新集合

```bash

ZUNIONSTORE dest numkeys key1 [key2 ...] [WEIGHTS w1 w2 ...] [AGGREGATE SUM|MIN|MAX]

ZINTERSTORE dest numkeys key1 [key2 ...] [WEIGHTS ...] [AGGREGATE ...]
```

- `ZUNIONSTORE`：并集（默认 sum）
- `ZINTERSTORE`：交集
- `WEIGHTS`：对每个集合的 score 加权
- `AGGREGATE`：相同 member 的 score 聚合规则

例：

```bash

ZUNIONSTORE result 2 zset1 zset2 WEIGHTS 1 2 AGGREGATE MAX
```

---
