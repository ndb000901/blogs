# Python基础-字典

## 1. 字典

Python 字典是**无序的键值对（key-value）集合**，也叫映射（mapping）类型。

> 每个 key 唯一，value 可以重复。字典是可变类型（mutable）。


## 2. 字典的定义方式

```python
# 1. 使用花括号定义
d1 = {"a": 1, "b": 2}

# 2. 使用 dict() 构造函数
d2 = dict(a=1, b=2)  # 键是合法的标识符

# 3. 空字典
d3 = {}

# 4. 通过列表/元组构造
d4 = dict([("a", 1), ("b", 2)])

# 5. 使用字典推导式
d5 = {k: k * 2 for k in range(3)}  # {0:0, 1:2, 2:4}
```

---

## 4. 常用操作

### 访问元素

```python
d = {"a": 1, "b": 2}
print(d["a"])      # 1
print(d.get("c"))  # None，不报错
print(d.get("c", 0))  # 默认值 0
```

### 添加/更新键值

```python
d["c"] = 3        # 添加新键
d["a"] = 10       # 修改已有键
```

### 删除元素

```python
d.pop("a")          # 删除指定键，返回值
d.pop("x", None)    # 不存在返回默认值
del d["b"]          # 用 del 删除
d.clear()           # 清空字典
```

### 遍历字典

```python
for k in d:                   # 遍历键
    print(k, d[k])

for k, v in d.items():        # 同时遍历键值
    print(k, v)

for v in d.values():          # 遍历值
    print(v)
```

### 合并字典

```python
# Python 3.5+
d1 = {"a": 1}
d2 = {"b": 2}
merged = {**d1, **d2}

# Python 3.9+
d1 |= d2  # 就地合并
```

---

## 5. 方法大全

| 方法                   | 说明                                |
|------------------------|-------------------------------------|
| `get(key, default)`    | 安全获取值                          |
| `setdefault(k, v)`     | 不存在则设置并返回                  |
| `update(dict)`         | 批量更新键值                        |
| `keys()`               | 所有键                              |
| `values()`             | 所有值                              |
| `items()`              | 所有键值对                          |
| `pop(k[, default])`    | 删除指定 key                        |
| `popitem()`            | 删除并返回最后一个键值对（3.7+）   |
| `clear()`              | 清空字典                            |
| `copy()`               | 浅拷贝                              |

---

## 6. 字典推导式（超常用）

```python
# 生成平方表
squares = {x: x * x for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# 条件过滤
filtered = {k: v for k, v in squares.items() if v % 2 == 0}
```

---

## 7. 字典的高级技巧

### `collections.defaultdict`

自动初始化值，常用于计数、分组等操作。

```python
from collections import defaultdict

d = defaultdict(int)
d["x"] += 1  # 不存在也不会报错，自动初始化为 0
```

### 字典视图是动态的

```python
d = {"a": 1}
keys_view = d.keys()
d["b"] = 2
print(list(keys_view))  # ['a', 'b']
```

### 嵌套字典

```python
student = {
    "name": "Tom",
    "scores": {
        "math": 90,
        "english": 85
    }
}
```

---


