# Lua基础-错误处理

在 Lua 中，**错误处理** 是通过 `error`、`pcall` 和 `xpcall` 等机制实现的，支持传统的异常处理流程，同时保持语言的简洁性。

---

## 1. 错误类型

Lua 中错误主要有两种类型：

1. **运行时错误（Runtime Error）**：如除以零、访问 `nil` 的字段等。
2. **语法错误（Syntax Error）**：如少了 `end` 或拼写错误。

---

## 2. 抛出错误：`error()`

```lua
error("出错啦！")
```

- 会立即终止程序运行，并抛出指定的错误信息。
- 通常配合条件判断使用。

```lua
function div(a, b)
  if b == 0 then
    error("除数不能为 0")
  end
  return a / b
end
```

---

## 3. 安全调用：`pcall`

### 3.1 基本用法

```lua
local ok, result = pcall(function()
  return 10 / nil
end)

if not ok then
  print("发生错误:", result)
end
```

- `pcall` 是 “protected call” 的缩写。
- 第一个返回值表示是否成功，第二个是函数的返回值或错误信息。
- `pcall` 会捕获错误而不会中断程序。

### 3.2 示例：加上错误提示

```lua
function safe_div(a, b)
  local ok, res = pcall(function() return a / b end)
  if ok then
    return res
  else
    print("发生错误:", res)
  end
end
```

---

## 4. 带 traceback 的错误：`xpcall`

```lua
function traceback(err)
  return debug.traceback("错误：" .. tostring(err), 2)
end

local ok, msg = xpcall(function()
  error("哎呀！")
end, traceback)

if not ok then
  print(msg)
end
```

- `xpcall` 是 “extended pcall”，可以在出错时传入一个自定义的错误处理函数。
- 常用于调试，因为 `traceback` 可以打印错误栈信息。

---

## 5. 封装异常机制

```lua
function try(func, catch)
  local ok, err = xpcall(func, debug.traceback)
  if not ok and catch then
    catch(err)
  end
end

try(
  function()
    error("测试异常")
  end,
  function(e)
    print("捕获到异常:", e)
  end
)
```

---

