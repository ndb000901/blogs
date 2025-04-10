# Python基础-Lambda表达式

## 1. Lambda 表达式

Lambda 表达式也叫**匿名函数（anonymous function）**，是一种**简洁地定义函数**的方法。  
通常用于定义一些**临时使用的小函数**，常与 `map()`、`filter()`、`sorted()` 等高阶函数搭配使用。

---

## 2. 基本语法

```python
lambda 参数1, 参数2, ... : 表达式
```

等价于：

```python
def 函数名(参数1, 参数2, ...):
    return 表达式
```

注意：`lambda` 只能写 **一个表达式**，不能包含多条语句（如赋值、循环等）。

---

## 3. 基础示例

```python
# 普通函数
def add(x, y):
    return x + y

# Lambda 写法
add_lambda = lambda x, y: x + y

print(add_lambda(3, 5))  # 输出 8
```

---

## 4. 常见应用场景

### 4.1 搭配 `map()`

```python
nums = [1, 2, 3, 4]
squares = list(map(lambda x: x * x, nums))
print(squares)  # [1, 4, 9, 16]
```

### 4.2 搭配 `filter()`

```python
nums = [1, 2, 3, 4, 5]
even = list(filter(lambda x: x % 2 == 0, nums))
print(even)  # [2, 4]
```

### 4.3 搭配 `sorted()` 自定义排序

```python
words = ['apple', 'banana', 'pear', 'grape']
sorted_words = sorted(words, key=lambda w: len(w))
print(sorted_words)  # ['pear', 'grape', 'apple', 'banana']
```

### 4.4 搭配 `reduce()`

```python
from functools import reduce

nums = [1, 2, 3, 4]
result = reduce(lambda x, y: x + y, nums)
print(result)  # 10
```

---


