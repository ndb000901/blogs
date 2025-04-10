# Python基础-JSON



## 1. JSON

**JSON（JavaScript Object Notation）** 是一种轻量级的数据交换格式，易于人类阅读和编写，也易于机器解析和生成。JSON 通常用于 **客户端与服务器之间的通信**，特别是在 Web 开发中非常流行。

### JSON 的基本数据类型：

- **字符串**：`"hello"`
- **数字**：`123`, `12.34`
- **布尔值**：`true`, `false`
- **数组**：`[1, 2, 3]`
- **对象（字典）**：`{"name": "Alice", "age": 25}`
- **空值**：`null`

---

## 2. `json` 模块概述

Python 的 `json` 模块可以处理 JSON 数据，提供了从 Python 对象到 JSON 格式的序列化（编码）和从 JSON 格式到 Python 对象的反序列化（解码）操作。

常用的两个函数：
- **`json.dumps()`**：将 Python 对象转换为 JSON 字符串（序列化）。
- **`json.loads()`**：将 JSON 字符串转换为 Python 对象（反序列化）。

此外，还有 **`json.dump()`** 和 **`json.load()`**，它们用于处理文件中的 JSON 数据。

---

## 3. `json.dumps()` - 序列化

`json.dumps()` 方法用于将 Python 对象转换为 JSON 字符串。这个方法返回的是 JSON 字符串。

### 语法：
```python
json.dumps(obj, 
           skipkeys=False, 
           ensure_ascii=True, 
           check_circular=True, 
           allow_nan=True, 
           cls=None, 
           indent=None, 
           separators=None, 
           default=None, 
           sort_keys=False, 
           use_decimal=False)
```

### 常用参数：
- **`obj`**：要转换的 Python 对象。
- **`indent`**：指定缩进级别，格式化 JSON 字符串。若为 `None`，则输出紧凑的 JSON。
- **`separators`**：一个 `(item_separator, key_separator)` 元组，用于分隔 JSON 对象中的键值对。默认为 `(', ', ': ')`，可以调整为更紧凑的格式。
- **`ensure_ascii`**：如果为 `True`（默认），所有非 ASCII 字符会被转义为 `\uXXXX` 的格式。
- **`sort_keys`**：如果为 `True`，则对字典中的键进行排序。

### 例子：

```python
import json

# 创建一个 Python 字典
data = {"name": "Alice", "age": 30, "city": "New York"}

# 将 Python 对象转换为 JSON 字符串
json_str = json.dumps(data)
print(json_str)
# 输出: {"name": "Alice", "age": 30, "city": "New York"}

# 格式化输出，带有缩进
json_str_pretty = json.dumps(data, indent=4)
print(json_str_pretty)
# 输出:
# {
#     "name": "Alice",
#     "age": 30,
#     "city": "New York"
# }
```

---

## 4. `json.loads()` - 反序列化

`json.loads()` 方法用于将 JSON 字符串转换为 Python 对象。该方法接受一个字符串并返回相应的 Python 对象。

### 语法：
```python
json.loads(s, 
           encoding=None, 
           cls=None, 
           object_hook=None, 
           parse_float=None, 
           parse_int=None, 
           parse_constant=None, 
           object_pairs_hook=None, 
           use_decimal=False)
```

### 常用参数：
- **`s`**：待转换的 JSON 字符串。
- **`object_hook`**：如果指定，解码时会将每个 JSON 对象转换为该函数的返回值。
- **`object_pairs_hook`**：与 `object_hook` 类似，但返回的字典会保持插入顺序。

### 例子：

```python
import json

# JSON 字符串
json_str = '{"name": "Alice", "age": 30, "city": "New York"}'

# 将 JSON 字符串转换为 Python 字典
data = json.loads(json_str)
print(data)
# 输出: {'name': 'Alice', 'age': 30, 'city': 'New York'}
```

---

## 5. `json.dump()` 和 `json.load()` - 文件操作

`json.dump()` 和 `json.load()` 分别用于将 Python 对象直接序列化到文件和从文件中反序列化 JSON 数据。

### `json.dump()` 语法：
```python
json.dump(obj, fp, 
          skipkeys=False, 
          ensure_ascii=True, 
          check_circular=True, 
          allow_nan=True, 
          cls=None, 
          indent=None, 
          separators=None, 
          default=None, 
          sort_keys=False, 
          use_decimal=False)
```

- **`fp`**：一个 `.write()` 方法可用的类文件对象（例如，文件句柄）。
- `json.dump()` 将 Python 对象序列化为 JSON，并将其写入文件。

### 例子：

```python
import json

# 要写入的 Python 对象
data = {"name": "Alice", "age": 30, "city": "New York"}

# 将 Python 对象写入 JSON 文件
with open("data.json", "w") as f:
    json.dump(data, f, indent=4)
```

---

### `json.load()` 语法：
```python
json.load(fp, 
          encoding=None, 
          cls=None, 
          object_hook=None, 
          parse_float=None, 
          parse_int=None, 
          parse_constant=None, 
          object_pairs_hook=None, 
          use_decimal=False)
```

- **`fp`**：包含 JSON 数据的类文件对象。

### 例子：

```python
import json

# 从文件读取 JSON 数据
with open("data.json", "r") as f:
    data = json.load(f)

print(data)
# 输出: {'name': 'Alice', 'age': 30, 'city': 'New York'}
```

---

## 6. JSON 与 Python 对象之间的映射

| Python 数据类型     | 对应 JSON 数据类型    |
|---------------------|-----------------------|
| `dict`              | `object`              |
| `list` / `tuple`    | `array`               |
| `str`               | `string`              |
| `int`               | `number`              |
| `float`             | `number`              |
| `True`              | `true`                |
| `False`             | `false`               |
| `None`              | `null`                |

### 特殊情况：JSON 中没有 `元组`（tuple）类型，但 `json` 模块会将 `元组` 序列化为 `列表`。

---

## 7. 注意事项

- **编码与解码中的特殊字符**：默认情况下，JSON 中的非 ASCII 字符会被转义为 `\uXXXX` 形式。可以通过设置 `ensure_ascii=False` 来保留原字符。

  ```python
  data = {"name": "张三"}
  json_str = json.dumps(data, ensure_ascii=False)
  print(json_str)  # {"name": "张三"}
  ```

- **浮动点数与 `NaN`**：JSON 格式不支持 `NaN`、`Infinity` 等特殊浮动点数值。Python 中的这些值默认会被转化为 `null` 或抛出错误。

---

