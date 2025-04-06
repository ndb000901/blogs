# Lua基础-循环

## 1. **`for` 循环**

`for` 循环有两种形式：数字 `for` 循环和通用 `for` 循环。

### 1.1 **数字 `for` 循环**

数字 `for` 循环用于循环一个固定次数。其基本语法格式为：

```lua
for var = start, stop, step do
    -- 代码块
end
```

- `var`：循环变量。
- `start`：循环的起始值。
- `stop`：循环的结束值。
- `step`：步长，默认为 `1`，表示每次递增。

**示例：**

```lua
for i = 1, 5 do
    print(i)
end
```


**带步长的示例：**

```lua
for i = 1, 10, 2 do
    print(i)
end
```

### 1.2 **通用 `for` 循环**

通用 `for` 循环可以迭代数组或表格中的元素，它通过迭代器进行控制。基本语法为：

```lua

for var_1, var_2, ..., var_n in iterators do
    -- 代码块
end
```

**示例：**

```lua
for i, v in ipairs({10, 20, 30}) do
    print(i, v)
end
```


---

## 2. **`while` 循环**

`while` 循环会在指定条件为 `true` 时不断重复执行代码块，直到条件变为 `false`。其基本语法为：

```lua
while condition do
    -- 代码块
end
```

- `condition`：条件表达式，循环会在其为 `true` 时继续执行。

**示例：**

```lua
local i = 1
while i <= 5 do
    print(i)
    i = i + 1
end
```

---

## 3. **`repeat...until` 循环**

`repeat...until` 循环与 `while` 循环类似，但它的判断条件在循环的末尾。即首先执行一次代码块，然后判断条件是否成立。如果条件为 `false`，则继续执行循环。其基本语法为：

```lua
repeat
    -- 代码块
until condition
```

- `condition`：循环判断条件，只有当条件为 `true` 时，循环才会结束。

**示例：**

```lua
local i = 1
repeat
    print(i)
    i = i + 1
until i > 5
```


此循环保证至少执行一次，即使条件在第一次迭代时就为 `false`，也会先执行一次代码块。

---

## 4. **`break` 和 `return`**

`break` 语句用于提前退出循环，通常用于在满足某些条件时终止循环。

### 4.1 **`break` 语句**

`break` 用于在任意条件下立即退出循环，无论条件是否满足。它可以用于 `for`、`while` 和 `repeat...until` 循环。

**示例：**

```lua
for i = 1, 10 do
    if i == 5 then
        break
    end
    print(i)
end
```


当 `i` 等于 5 时，`break` 语句使循环提前终止。

### 4.2 **`return` 语句**

`return` 语句不仅用于退出函数，也可以在循环中使用来结束整个函数的执行。

**示例：**

```lua
function test()
    for i = 1, 5 do
        if i == 3 then
            return
        end
        print(i)
    end
end

test()
```


当 `i` 等于 3 时，`return` 语句终止了整个函数的执行，后续的循环被跳过。

---

## 5. **`continue` 语句（Lua 本身不支持）**

虽然 Lua 原生没有 `continue` 语句，但可以通过 `if` 语句来模拟 `continue`，即跳过当前的循环迭代，继续下一个迭代。

**模拟 `continue`：**

```lua
for i = 1, 10 do
    if i == 5 then
        -- 模拟 continue：跳过 i 等于 5 的这次迭代
        goto continue
    end
    print(i)
    
    ::continue::
end
```


## 6. **标签和 `goto` 语句**

Lua 中还支持使用 `goto` 来跳转到代码中的标签位置。`goto` 可以在 `for`、`while` 和 `repeat` 循环中用来提前跳出或跳到某个位置。

**示例：**

```lua
for i = 1, 10 do
    if i == 5 then
        goto skip
    end
    print(i)
    
    ::skip::
end
```

在该示例中，当 `i == 5` 时，`goto skip` 使得循环跳过当前的迭代。

---
