# Lua基础-迭代器

在 Lua 中，**迭代器（iterator）** 是一种用于遍历数据结构（如表、数组、文件等）的机制。它本质上是一个函数，每次调用返回**下一个元素**，直到遍历完成。

---

## 1. 迭代器的三种类型

### 1.1 内置迭代器：`pairs()` 和 `ipairs()`

#### `pairs(table)`
遍历**所有键值对**（包括非整数索引、非连续索引）：
```lua
local t = { name = "Lua", version = 5.4, [10] = "ten" }
for k, v in pairs(t) do
  print(k, v)
end
```

#### `ipairs(table)`
遍历**按整数递增索引从1开始的部分**，直到遇到 `nil`：
```lua
local t = { "a", "b", "c", nil, "d" }
for i, v in ipairs(t) do
  print(i, v)
end
-- 只输出 a, b, c，遇到 nil 后终止
```

---


## 2. 自定义迭代器


### 示例：自定义数字范围迭代器

```lua
function range_iter(state, current)
  if current < state.max then
    current = current + 1
    return current
  end
end

function range(start, stop)
  return range_iter, { max = stop }, start - 1
end

for i in range(1, 5) do
  print(i)
end
-- 输出：1 2 3 4 5
```

- `range()` 返回三个值：迭代函数、状态、初始值。
- `range_iter` 每次返回下一个值。

---

## 3. 无状态迭代器（闭包迭代器）

使用闭包实现无状态迭代器（不显式传递状态）：

```lua
function values(t)
  local i = 0
  return function()
    i = i + 1
    return t[i]
  end
end

for v in values({10, 20, 30}) do
  print(v)
end
```

---

## 4.文件迭代器（I/O）

```lua
for line in io.lines("test.txt") do
  print(line)
end
```

- `io.lines` 返回一个迭代器函数，每次读取一行。

---

## 5. 使用 `next()` 手动迭代表

```lua
local t = { a = 1, b = 2 }
local k, v = next(t, nil)
while k do
  print(k, v)
  k, v = next(t, k)
end
```
