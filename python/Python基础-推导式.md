# Python基础-推导式


## 1. 推导式

**推导式（Comprehension）** 是 Python 中用于创建集合类型（如列表、集合、字典）的简洁语法。它是 Python 中一种声明式的操作方式，提供了 **简洁、优雅** 的方式来创建新的集合类型。

推导式通常由 **表达式** 和 **可迭代对象** 组成，允许在单行内通过一定的规则生成新的集合对象。

---

## 2. 列表推导式（List Comprehension）

### 基本语法

```python
[表达式 for 元素 in 可迭代对象 if 条件]
```

### 例子：

```python
# 获取 1 到 10 之间的平方数
squares = [x**2 for x in range(1, 11)]
print(squares)  # [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

> 解释：  
> `x**2` 是表达式，`for x in range(1, 11)` 是循环部分。

---

### 带条件的推导式

```python
# 获取 1 到 10 之间的偶数
evens = [x for x in range(1, 11) if x % 2 == 0]
print(evens)  # [2, 4, 6, 8, 10]
```

> 解释：  
> 在 `for` 后面加上 `if` 条件过滤满足的元素。

---

## 3. 集合推导式（Set Comprehension）

集合推导式和列表推导式类似，只不过使用 `{}` 来包围表达式。

### 基本语法

```python
{表达式 for 元素 in 可迭代对象 if 条件}
```

### 例子：

```python
# 获取 1 到 10 之间的偶数集合
evens_set = {x for x in range(1, 11) if x % 2 == 0}
print(evens_set)  # {2, 4, 6, 8, 10}
```

> 由于集合的特点是 **不允许重复元素**，所以即使生成过程中有重复元素，最终也只会保留唯一元素。

---

## 4. 字典推导式（Dictionary Comprehension）

字典推导式用于创建字典，**键值对的表达式**组成字典的 **key** 和 **value**。

### 基本语法

```python
{键表达式: 值表达式 for 元素 in 可迭代对象 if 条件}
```

### 例子：

```python
# 创建数字和它们的平方的字典
square_dict = {x: x**2 for x in range(1, 6)}
print(square_dict)  # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

> 解释：  
> `x: x**2` 生成键值对，`for x in range(1, 6)` 遍历范围。

---

### 带条件的字典推导式

```python
# 生成字典，键为 1 到 10 之间的奇数，值为这些奇数的平方
odd_square_dict = {x: x**2 for x in range(1, 11) if x % 2 != 0}
print(odd_square_dict)  # {1: 1, 3: 9, 5: 25, 7: 49, 9: 81}
```

---

## 5. 生成器推导式（Generator Comprehension）

生成器推导式和列表推导式类似，但它使用 **圆括号** `()` 而不是方括号 `[]`，并且生成器推导式是 **惰性求值** 的，即在迭代时生成元素，而不是一次性创建一个完整的列表。

### 基本语法

```python
(表达式 for 元素 in 可迭代对象 if 条件)
```

### 例子：

```python
# 获取 1 到 10 之间的平方数生成器
gen = (x**2 for x in range(1, 11))
for val in gen:
    print(val)
```

> 解释：  
> `x**2` 为表达式，`(x for x in range(1, 11))` 创建了一个生成器。

生成器推导式相比列表推导式更节省内存，因为它不是一次性创建所有的元素，而是在需要时才生成一个元素。

---

## 6. 嵌套推导式

推导式也可以是嵌套的，即在一个推导式中使用另一个推导式。这在处理复杂的集合数据时很有用。

### 例子：嵌套列表推导式

```python
# 生成一个二维列表的转置
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
transpose = [[row[i] for row in matrix] for i in range(3)]
print(transpose)  # [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
```

> 解释：  
> `row[i] for row in matrix` 表示获取每行的第 `i` 列元素，外部推导式按列索引生成新列表。

---


