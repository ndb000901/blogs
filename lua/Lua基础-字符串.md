# Lua基础-字符串

在 Lua 中，**字符串**是非常基础和重要的类型之一，Lua 提供了丰富的字符串操作功能。Lua 中的字符串是不可变的（immutable），即一旦创建后，其内容不能被修改。

## 1. **字符串的定义**

### 1.1 **单引号和双引号**

在 Lua 中，字符串可以用单引号或双引号来定义，功能是相同的。

```lua
local str1 = 'Hello'
local str2 = "World"
```

### 1.2 **多行字符串**

Lua 通过双方括号 (`[[]]`) 来支持多行字符串，字符串中的换行符、制表符等会被保留。

```lua
local multiline_str = [[
Hello,
This is a multi-line
string in Lua.
]]
print(multiline_str)
```


### 1.3 **转义字符**

字符串中也可以包含转义字符，通过反斜杠（`\`）来实现。

- `\\`：表示反斜杠。
- `\n`：表示换行符。
- `\t`：表示制表符。
- `\'`：表示单引号。
- `\"`：表示双引号。

```lua
local str = "Hello\nWorld\t!"
print(str)
```

## 2. **字符串操作**

Lua 提供了多种内置函数来操作字符串。

### 2.1 **拼接字符串**

字符串拼接使用 `..` 运算符。

```lua
local str1 = "Hello"
local str2 = "World"
local result = str1 .. " " .. str2
print(result)  -- 输出 Hello World
```

### 2.2 **获取字符串长度**

使用 `#` 运算符获取字符串的长度。

```lua
local str = "Hello"
print(#str)  -- 输出 5
```

### 2.3 **字符串比较**

Lua 提供了 `==` 运算符来比较两个字符串是否相等。

```lua
local str1 = "Hello"
local str2 = "Hello"
local str3 = "World"

print(str1 == str2)  -- 输出 true
print(str1 == str3)  -- 输出 false
```

### 2.4 **获取子字符串**

使用 `string.sub` 函数来获取子字符串。该函数接受起始索引和结束索引作为参数。

```lua
local str = "Hello, Lua!"
local sub_str = string.sub(str, 1, 5)
print(sub_str)  -- 输出 Hello
```

- 第一个参数是原始字符串，第二和第三个参数是起始和结束位置（从 1 开始）。

### 2.5 **查找子字符串**

使用 `string.find` 来查找子字符串的起始和结束位置。

```lua
local str = "Hello, Lua!"
local start_pos, end_pos = string.find(str, "Lua")
print(start_pos, end_pos)  -- 输出 8 10
```

- `string.find` 返回子字符串的起始和结束位置，如果找不到，返回 `nil`。

### 2.6 **替换子字符串**

使用 `string.gsub` 函数替换字符串中的部分内容。

```lua
local str = "Hello, Lua!"
local new_str = string.gsub(str, "Lua", "World")
print(new_str)  -- 输出 Hello, World!
```

- `string.gsub` 会替换所有匹配的子字符串，如果需要替换单个匹配的子字符串，可以使用额外的参数限制替换次数。

### 2.7 **转化大小写**

- **`string.upper`**：将字符串转换为大写。
- **`string.lower`**：将字符串转换为小写。

```lua
local str = "Hello, Lua!"
print(string.upper(str))  -- 输出 HELLO, LUA!
print(string.lower(str))  -- 输出 hello, lua!
```

### 2.8 **去除空格**

使用 `string.match` 可以去除字符串两端的空格。

```lua
local str = "   Hello, Lua!   "
local trimmed_str = string.match(str, "^%s*(.-)%s*$")
print(trimmed_str)  -- 输出 Hello, Lua!
```

- `^%s*` 匹配字符串开头的空格，`%s*$` 匹配结尾的空格，`(.-)` 非贪婪匹配字符串主体部分。

---

## 3. **格式化字符串**

Lua 提供了 `string.format` 函数来格式化字符串，它的使用方式与 C 语言中的 `printf` 类似。

```lua
local name = "Lua"
local version = 5.4
local str = string.format("Welcome to %s %0.1f", name, version)
print(str)  -- 输出 Welcome to Lua 5.4
```

- `string.format` 可以格式化数字、字符串等，可以用于生成具有特定格式的字符串。

---

## 4. **字符串的模式匹配**

Lua 提供了基于正则表达式的模式匹配功能。通过 `string.match`、`string.gmatch` 和 `string.find` 可以使用模式来匹配字符串。

### 4.1 **`string.match`**

`string.match` 返回第一个匹配的子字符串。

```lua
local str = "Hello, Lua!"
local word = string.match(str, "Lua")
print(word)  -- 输出 Lua
```

### 4.2 **`string.gmatch`**

`string.gmatch` 返回一个迭代器，迭代所有匹配的子字符串。

```lua
local str = "apple, banana, cherry"
for word in string.gmatch(str, "%a+") do
    print(word)
end
```

### 4.3 **`string.find`**

`string.find` 用于查找匹配子字符串的起始和结束位置。

```lua
local str = "Hello, Lua!"
local start_pos, end_pos = string.find(str, "Lua")
print(start_pos, end_pos)  -- 输出 8 10
```

---

## 5. **常见的字符串模式**

Lua 的字符串模式具有与正则表达式相似的语法，常见的模式包括：

- `%d`：匹配一个数字字符。
- `%a`：匹配一个字母字符。
- `%s`：匹配一个空白字符（如空格、制表符、换行符等）。
- `%w`：匹配一个字母数字字符。
- `.`：匹配任何单个字符（除了 `\n`）。
- `*`：匹配零次或多次前面的字符。
- `+`：匹配一次或多次前面的字符。
- `-`：非贪婪匹配。


