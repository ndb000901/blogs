# Python基础-循环

## 1. `for` 循环

### 基本语法：

```python
for item in iterable:
    # 循环体
```

**例子：**

```python
for i in range(3):
    print(i)
```

输出：
```
0
1
2
```

### 常用可迭代对象：

- 字符串、列表、元组、字典、集合
- `range(start, stop, step)`
- 自定义迭代器 / 生成器

### 遍历字典示例：

```python
person = {"name": "Alice", "age": 30}
for key, value in person.items():
    print(f"{key} = {value}")
```

---

## 2. `while` 循环

### 基本语法：

```python
while condition:
    # 循环体
```

**例子：**

```python
count = 0
while count < 3:
    print(count)
    count += 1
```

---

## 3. 循环控制语句

| 语句       | 作用                       |
|------------|----------------------------|
| `break`    | 立即终止整个循环            |
| `continue` | 跳过本次循环，进入下一次迭代 |
| `else`     | 循环正常结束（非 `break`）时执行 |

### `break` 示例：

```python
for i in range(5):
    if i == 3:
        break
    print(i)
```

输出：
```
0
1
2
```

### `continue` 示例：

```python
for i in range(5):
    if i == 3:
        continue
    print(i)
```

输出：
```
0
1
2
4
```

### `else` 示例：

```python
for i in range(5):
    print(i)
else:
    print("循环正常结束")
```

注意：**只有没有遇到 `break` 时才会执行 `else`。**

---


