# Redis基础-列表


## 1. Redis List

Redis 列表是一个**双端链表结构的有序集合**：

- 每个元素是一个字符串。
- 元素按插入顺序排序。
- 可以从两端进行推入/弹出操作（左边、右边）。
- 支持按索引访问、范围切片、阻塞式读写等。

---

## 2. 底层实现

- 小数据量时使用 **ziplist（压缩列表）**。
- 数据量大或存在大字符串时，自动切换为 **quicklist**（快速链表）。
- quicklist = 多个 ziplist + 双向链表特性，兼顾空间与性能。

---

## 3. 核心命令

### 3.1 添加元素

```bash

LPUSH key value [value ...]   # 从左侧推入（头部）
RPUSH key value [value ...]   # 从右侧推入（尾部）
```

- 支持一次推入多个元素。
- 若 key 不存在，将创建新列表。

例子：

```bash

LPUSH mylist a b c   # 列表变成 [c, b, a]
```

---

### 3.2 添加元素（仅当 key 存在）

```bash

LPUSHX key value
RPUSHX key value
```

- key 不存在时不会创建新列表。

---

### 3.3 弹出元素

```bash

LPOP key      # 弹出最左侧（头部）元素
RPOP key      # 弹出最右侧（尾部）元素
```

---

### 3.4 阻塞式弹出

```bash

BLPOP key [key2 ...] timeout
BRPOP key [key2 ...] timeout
```

- 支持多个 key，哪个有值就先返回。
- timeout = 0 表示永久阻塞。

---

### 3.5 移动元素

```bash

LMOVE source destination LEFT|RIGHT LEFT|RIGHT
```

- 支持从一个列表的任意一侧弹出，再插入到另一个列表的任意一侧。

例：

```bash

LMOVE list1 list2 LEFT RIGHT
```

---

### 3.6 查看元素

```bash

LRANGE key start stop
```

- 获取指定范围的元素（闭区间）。
- `-1` 表示最后一个元素。

例：

```bash

LRANGE mylist 0 -1   # 获取整个列表
```

---

### 3.7 获取指定位置元素

```bash

LINDEX key index
```

- 获取指定下标位置的元素，支持负数表示倒数。

---

### 3.8 修改指定位置元素

```bash

LSET key index value
```

- 设置指定索引的值，若越界会报错。

---

### 3.9 删除元素

```bash

LREM key count value
```

- 从列表中删除指定值的元素。
- `count > 0`：从头向尾删除
- `count < 0`：从尾向头删除
- `count = 0`：删除所有匹配项

---

### 3.10 修剪列表

```bash

LTRIM key start stop
```

- 保留指定范围的元素，其余全部删除。

例：

```bash

LTRIM mylist 0 4    # 只保留前 5 个元素
```

---

### 3.11 获取长度

```bash

LLEN key
```

---

## 4. 阻塞流式读取

```bash

BLMOVE source destination LEFT|RIGHT LEFT|RIGHT timeout
```

- 等价于阻塞式的 `LMOVE`，支持双端阻塞。

---

