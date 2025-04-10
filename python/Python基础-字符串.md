# Python基础-字符串

## 1. 字符串

字符串（`str`）是由**一系列字符组成的不可变序列**，用于存储和处理文本数据。

```python
s = "Hello, World!"
```

---

## 2. 创建字符串

### 单引号、双引号、三引号

```python
s1 = 'hello'
s2 = "hello"
s3 = '''多行
字符串'''
s4 = """也可以
换行"""
```

### 空字符串

```python
empty = ''
```

---

## 3. 字符串常见操作

### 3.1 获取长度

```python
len("hello")  # 5
```

---

### 3.2 索引和切片

```python
s = "abcdef"
print(s[0])     # 'a'
print(s[-1])    # 'f'
print(s[1:4])   # 'bcd'
print(s[:3])    # 'abc'
print(s[::2])   # 'ace'
```

---

### 3.3 遍历字符串

```python
for ch in "hi":
    print(ch)
```

---

### 3.4 常用方法

| 方法名           | 功能                             |
|------------------|----------------------------------|
| `s.lower()`      | 全部转小写                       |
| `s.upper()`      | 全部转大写                       |
| `s.strip()`      | 去除两端空白符                   |
| `s.startswith(x)`| 是否以 `x` 开头                  |
| `s.endswith(x)`  | 是否以 `x` 结尾                  |
| `s.find(x)`      | 返回第一次出现位置，不存在返回 -1 |
| `s.replace(a,b)` | 替换所有 `a` 为 `b`              |
| `s.split(',')`   | 按分隔符切割成列表               |
| `','.join(list)` | 用 `,` 把列表拼成字符串           |

```python
"hello world".replace("world", "python")  # 'hello python'
```

---

### 3.5 格式化字符串

#### f-string

```python
name = "Alice"
age = 20
print(f"My name is {name}, age is {age}")
```

#### format 方法

```python
print("My name is {}, age is {}".format(name, age))
```

#### 百分号格式化

```python
print("My name is %s, age is %d" % (name, age))
```

---

## 4. 字符串是不可变类型

```python
s = "hello"
s[0] = 'H'  # TypeError
```

虽然不能修改，但可以重新赋值：

```python
s = "H" + s[1:]
```

---

## 5. 字符串编码和解码

### 字符串 和 字节 的区别：

- `str` 是 **Unicode 字符串**
- `bytes` 是 **原始字节数据**

### 编码为 bytes：

```python
s = "你好"
b = s.encode("utf-8")  # b'\xe4\xbd\xa0\xe5\xa5\xbd'
```

### 解码为 str：

```python
s2 = b.decode("utf-8")
```

---

## 6. 字符串的判断方法

| 方法            | 说明                       |
|-----------------|----------------------------|
| `isalpha()`     | 是否全部为字母             |
| `isdigit()`     | 是否全部为数字             |
| `isalnum()`     | 是否为字母数字组合         |
| `isspace()`     | 是否全是空白字符           |
| `istitle()`     | 是否为标题格式             |

```python
"abc123".isalnum()  # True
"  \n\t".isspace()  # True
```

---

## 7. strip / lstrip / rstrip 示例

```python
s = "  hello \n"
print(s.strip())    # "hello"
print(s.lstrip())   # "hello \n"
print(s.rstrip())   # "  hello"
```

---

## 8. 字符串比较

按字符的 Unicode 顺序逐个比较：

```python
"abc" < "abd"     # True
"A" < "a"         # True，因为 A 的 Unicode 值较小
```

---

## 9. 字符串拼接

### 不推荐频繁使用 `+`

```python
s = ""
for i in range(1000):
    s += "a"  # 效率低，生成新对象
```

### 推荐使用 `join`

```python
s = "".join(["a"] * 1000)
```

---
