# Python基础-元组

## 1. 元组（Tuple）

元组是 Python 中的**内置数据类型之一**，和列表类似，用来存储一组有序的数据，但有一个显著区别：

> **元组是不可变的**（immutable），也就是说，创建之后就不能修改、添加或删除元素。

---

## 2. 元组的定义方式

```python
# 1. 基础写法
t1 = (1, 2, 3)

# 2. 可以省略括号（不推荐）
t2 = 1, 2, 3

# 3. 空元组
t3 = ()

# 4. 单元素元组（必须加逗号！）
t4 = (42,)   # 否则会被当成 int 类型

# 5. 使用 tuple() 构造
t5 = tuple([1, 2, 3])  # 从列表创建元组
```

---

## 3. 元组的不可变性

```python
t = (1, 2, 3)
# t[0] = 99  # 报错：'tuple' object does not support item assignment
```

虽然元组本身不可变，但如果**元组中包含可变对象（如列表）**，该对象的内容是可以变的：

```python
t = ([1, 2], 3)
t[0][0] = 99
print(t)  # ([99, 2], 3)
```

---


## 4. 元组的基本操作

### 索引 & 切片

```python
t = (10, 20, 30, 40)

print(t[0])      # 10
print(t[-1])     # 40
print(t[1:3])    # (20, 30)
```

### 遍历

```python
for item in t:
    print(item)

# 带索引遍历
for i, v in enumerate(t):
    print(i, v)
```

### 常用方法

```python
t = (1, 2, 3, 2)

print(t.count(2))  # 2：2出现的次数
print(t.index(3))  # 2：元素3的索引位置
```

---

## 5. 元组打包与解包

### 打包（Packing）

```python
info = "Tom", 18, "男"  # 自动打包为元组
```

### 解包（Unpacking）

```python
name, age, gender = info
print(name, age, gender)
```

### 解包进阶：星号 * 解包

```python
t = (1, 2, 3, 4, 5)

a, *b, c = t
print(a)  # 1
print(b)  # [2, 3, 4]
print(c)  # 5
```

---


## 6. 元组作为字典键或集合元素

```python
# 可以作为 key（前提是元素本身也不可变）
coords = {(1, 2): "A", (3, 4): "B"}

print(coords[(1, 2)])  # A
```

注意：下面这种是错误的，包含列表（可变）会报错：

```python
# 错误示例
# my_dict = {([1, 2], 3): "invalid"}  # 报错
```

---

## 7. 函数返回多个值（元组打包）

```python
def get_user():
    return "Alice", 25

name, age = get_user()
print(name, age)
```

---


