# Python基础-正则表达式

正则表达式（Regular Expression，简称 `regex`）是一种用于字符串模式匹配、搜索和替换的工具。它可以帮助我们高效地对字符串进行复杂的匹配操作。Python 内置了 `re` 模块来支持正则表达式操作。

---

## 1. 基本概念

正则表达式是一种特殊的文本字符串，它可以描述一个搜索模式。这个模式可以用于匹配、查找和操作字符串数据。正则表达式中的每个字符或符号都代表不同的匹配规则。

### 正则表达式基本组成
- **普通字符**：表示字符本身，如字母、数字、空格等。
- **特殊字符（元字符）**：这些字符具有特殊意义，用于构建复杂的匹配规则。

常用的元字符包括：
- `.`：匹配任意单个字符（除换行符外）
- `^`：匹配字符串的开始
- `$`：匹配字符串的结束
- `[]`：字符集，表示匹配括号内的任意一个字符
- `|`：表示“或”操作，匹配左边或右边的表达式
- `()`：分组，用来匹配括号中的内容，并将其作为一个整体
- `\`：转义字符，用于匹配特殊字符本身或表示特殊意义，如 `\d` 表示数字

---

## 2. 常用正则表达式

### 2.1 **元字符详解**

#### 2.1.1 点号（`.`）
匹配除了换行符 `\n` 外的任何单个字符。
```python
import re
# 匹配 "a"、"b"、"c" 等字符
pattern = re.compile("a.c")
result = pattern.match("abc")  # 返回匹配成功
```

#### 2.1.2 方括号（`[]`）
表示字符集，匹配字符集中的任意一个字符。可以用 `-` 来指定字符范围。
```python
import re
# 匹配 'a' 或 'b'
pattern = re.compile("[ab]")
result = pattern.match("a")  # 返回匹配成功
```
- `[a-z]`：匹配小写字母
- `[A-Z]`：匹配大写字母
- `[0-9]`：匹配数字

#### 2.1.3 脱字符（`^`）
放在字符集的开头时表示“取反”，即匹配不在字符集中的字符。
```python
import re
# 匹配不包含数字的字符
pattern = re.compile("[^0-9]")
result = pattern.match("a")  # 返回匹配成功
```

#### 2.1.4 竖线（`|`）
表示“或”操作，匹配左边或右边的表达式。
```python
import re
# 匹配 "cat" 或 "dog"
pattern = re.compile("cat|dog")
result = pattern.match("dog")  # 返回匹配成功
```

#### 2.1.5 小括号（`()`）
用于分组，括起来的部分被视为一个整体，用于捕获或组合多个子表达式。
```python
import re
# 捕获 'cat' 或 'dog' 的组合
pattern = re.compile("(cat|dog)fish")
result = pattern.match("catfish")  # 返回匹配成功
```

#### 2.1.6 反斜杠（`\`）
用于转义特殊字符或者匹配特定的字符。
- `\d`：匹配任何数字，等同于 `[0-9]`
- `\D`：匹配任何非数字字符
- `\w`：匹配任何字母、数字、下划线，等同于 `[a-zA-Z0-9_]`
- `\W`：匹配任何非字母、非数字、非下划线字符
- `\s`：匹配任何空白字符（包括空格、制表符等）
- `\S`：匹配任何非空白字符

---

### 2.2 **数量词**

数量词用于限定字符出现的次数。常用的数量词有：
- `*`：匹配前面的字符零次或多次
- `+`：匹配前面的字符一次或多次
- `?`：匹配前面的字符零次或一次
- `{n}`：匹配前面字符刚好出现 n 次
- `{n,}`：匹配前面字符至少出现 n 次
- `{n,m}`：匹配前面字符出现 n 到 m 次

```python
import re
# 匹配任意长度的数字
pattern = re.compile(r"\d*")
result = pattern.match("12345")  # 返回匹配成功
```

---

### 2.3 **边界匹配**

#### 3.1 `^` 和 `$`
- `^`：匹配字符串的开始
- `$`：匹配字符串的结束

```python
import re
# 匹配以 "Hello" 开头的字符串
pattern = re.compile("^Hello")
result = pattern.match("Hello, World")  # 返回匹配成功
```

---

## 3. `re` 模块常用方法

### 3.1 **`re.match()`**
`match()` 方法用于从字符串的起始位置开始匹配。如果匹配成功，返回一个匹配对象；否则返回 `None`。

```python
import re
result = re.match(r"hello", "hello world")
if result:
    print("Matched:", result.group())  # 输出：Matched: hello
```

### 3.2 **`re.search()`**
`search()` 方法用于搜索整个字符串中第一个匹配的子串，返回一个匹配对象。

```python
import re
result = re.search(r"world", "hello world")
if result:
    print("Found:", result.group())  # 输出：Found: world
```

### 3.3 **`re.findall()`**
`findall()` 方法返回所有匹配的字符串，返回一个列表。

```python
import re
result = re.findall(r"\d+", "12 abc 34 56")
print(result)  # 输出：['12', '34', '56']
```

### 3.4 **`re.finditer()`**
`finditer()` 方法返回一个迭代器，其中包含所有匹配的对象。

```python
import re
result = re.finditer(r"\d+", "12 abc 34 56")
for match in result:
    print(match.group())  # 输出：12 34 56
```

### 3.5 **`re.sub()`**
`sub()` 方法用于替换字符串中的匹配部分。

```python
import re
result = re.sub(r"\d+", "#", "abc 123 xyz 456")
print(result)  # 输出：abc # xyz #
```

### 3.6 **`re.split()`**
`split()` 方法根据匹配的模式来分割字符串。

```python
import re
result = re.split(r"\s+", "Hello   world   Python")
print(result)  # 输出：['Hello', 'world', 'Python']
```

