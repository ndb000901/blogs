# Python基础-列表


## 1. 列表

列表（List）是 Python 中最常用的数据结构之一，用来存储**一组有序的数据集合**，支持**增删改查、重复元素、嵌套、多类型混存**等。

列表是**可变的**（mutable）对象。

---

## 2. 列表的创建方式

```python
# 1. 使用中括号 []
nums = [1, 2, 3]

# 2. 使用 list() 构造函数
chars = list("hello")  # ['h', 'e', 'l', 'l', 'o']

# 3. 空列表
empty = []

# 4. 嵌套列表
matrix = [[1, 2], [3, 4]]
```

---

## 3. 访问列表元素

```python
nums = [10, 20, 30, 40]

print(nums[0])   # 访问第一个元素：10
print(nums[-1])  # 访问最后一个元素：40
```

---

## 4. 列表的增删改查操作

### 添加元素

```python
lst = [1, 2]

lst.append(3)            # 添加到末尾
lst.insert(1, 99)        # 在索引1插入元素
lst.extend([4, 5])       # 扩展多个元素

print(lst)  # [1, 99, 2, 3, 4, 5]
```

### 删除元素

```python
lst = [1, 2, 3, 4, 5]

lst.remove(3)        # 删除第一个值为3的元素
x = lst.pop()        # 弹出最后一个元素
y = lst.pop(1)       # 弹出索引1的元素
del lst[0]           # 删除索引0的元素
lst.clear()          # 清空列表

print(lst)
```

### 修改元素

```python
lst = [10, 20, 30]
lst[1] = 99
print(lst)  # [10, 99, 30]
```

### 查找元素

```python
lst = [1, 2, 3, 2]

print(lst.index(2))     # 找到第一个 2 的索引：1
print(lst.count(2))     # 2 出现了 2 次
print(3 in lst)         # True
```

---

## 5. 列表遍历方式

```python
lst = [10, 20, 30]

for x in lst:
    print(x)

# 带索引遍历
for i, v in enumerate(lst):
    print(i, v)
```

---

## 6. 列表切片

```python
lst = [0, 1, 2, 3, 4, 5]

print(lst[1:4])     # [1, 2, 3]
print(lst[:3])      # [0, 1, 2]
print(lst[::2])     # [0, 2, 4]
print(lst[::-1])    # 反转列表 [5, 4, 3, 2, 1, 0]
```

---

## 7. 列表常用方法总结

| 方法             | 作用                                |
|------------------|-------------------------------------|
| `append(x)`      | 末尾添加元素 x                      |
| `insert(i, x)`   | 在索引 i 插入元素 x                 |
| `extend(iter)`   | 扩展多个元素                        |
| `remove(x)`      | 删除第一个值为 x 的元素             |
| `pop([i])`       | 弹出指定索引元素，默认最后一个      |
| `clear()`        | 清空列表                            |
| `index(x)`       | 获取第一个 x 的索引                 |
| `count(x)`       | 统计 x 出现次数                     |
| `sort()`         | 原地排序（默认升序）                |
| `sorted()`       | 返回一个新排序列表                  |
| `reverse()`      | 原地反转                            |
| `copy()`         | 浅拷贝                              |

---

## 8. 列表推导式（List Comprehension）

语法简洁，效率高，用于快速构建列表。

```python
squares = [x**2 for x in range(5)]        # [0, 1, 4, 9, 16]
even = [x for x in range(10) if x % 2 == 0]
pairs = [(x, y) for x in range(2) for y in range(2)]
```

---

## 9. 嵌套列表与二维列表

```python
matrix = [[1, 2], [3, 4]]

# 访问元素
print(matrix[1][0])  # 输出 3

# 遍历二维列表
for row in matrix:
    for item in row:
        print(item)
```

---

## 10. 浅拷贝 vs 深拷贝

```python
import copy

lst1 = [[1, 2], [3, 4]]
lst2 = lst1.copy()           # 浅拷贝：只复制第一层
lst3 = copy.deepcopy(lst1)   # 深拷贝：完全复制

lst1[0][0] = 99
print(lst2)  # [[99, 2], [3, 4]]
print(lst3)  # [[1, 2], [3, 4]]
```

---


