# Lua基础-元素

在 Lua 中，**元表（metatable）** 是一张特殊的表，用来改变另一张表的行为。它可以“重载运算符”、控制访问、模拟面向对象等，是 Lua 元编程的核心机制之一。

---

## 1. 元表

普通的 Lua 表是没有行为的，仅存储数据。元表可以为表添加**行为（行为由元方法定义）**。

比如，元表可以让你定义两个表相加时的行为，或在访问表中不存在的字段时触发某些操作。

---

## 2. 设置和获取元表

```lua
-- 创建一个表
local t = {}

-- 创建元表
local mt = {}

-- 设置元表
setmetatable(t, mt)

-- 获取元表
local mt2 = getmetatable(t)
```

---

## 3. 常用元方法

元表中的“关键字段”叫做**元方法（metamethod）**，是具有特定意义的键。

| 元方法名       | 触发条件                                 | 示例用途                       |
|----------------|------------------------------------------|--------------------------------|
| `__index`      | 访问不存在的字段                         | 实现继承、默认值               |
| `__newindex`   | 向不存在字段赋值                         | 拦截写操作、只读表             |
| `__add`        | `a + b`                                  | 重载加法                       |
| `__sub`        | `a - b`                                  | 重载减法                       |
| `__mul`        | `a * b`                                  | 重载乘法                       |
| `__div`        | `a / b`                                  | 重载除法                       |
| `__mod`        | `a % b`                                  | 重载取模                       |
| `__pow`        | `a ^ b`                                  | 重载幂运算                     |
| `__eq`         | `a == b`                                 | 重载相等比较                   |
| `__lt`         | `a < b`                                  | 重载小于比较                   |
| `__le`         | `a <= b`                                 | 重载小于等于                   |
| `__tostring`   | `tostring(a)`                            | 自定义字符串输出               |
| `__call`       | `a()`                                    | 让表像函数一样被调用           |
| `__metatable`  | 屏蔽 `getmetatable()`                    | 提供封装/保护元表             |

---

## 4. 示例

### 4.1 `__index`：模拟默认字段、继承

```lua
local default = {x = 0, y = 0}
local t = setmetatable({}, { __index = default })

print(t.x)  -- 输出 0
```

### 4.2 `__newindex`：拦截赋值

```lua
local t = {}
setmetatable(t, {
  __newindex = function(tbl, key, value)
    print("设置了新字段", key, value)
    rawset(tbl, key, value)
  end
})
t.a = 10  -- 会触发 __newindex
```

### 4.3 `__add`：重载加法

```lua
local mt = {
  __add = function(a, b)
    return { x = a.x + b.x, y = a.y + b.y }
  end
}

local a = setmetatable({x = 1, y = 2}, mt)
local b = setmetatable({x = 3, y = 4}, mt)
local c = a + b

print(c.x, c.y)  --> 4 6
```

### 4.4 `__tostring`：打印美化

```lua
local t = setmetatable({name = "Lua"}, {
  __tostring = function(tbl)
    return "Hello, " .. tbl.name
  end
})
print(t)  --> Hello, Lua
```

### 4.5 `__call`：表当函数用

```lua
local t = setmetatable({}, {
  __call = function(_, a, b)
    return a + b
  end
})
print(t(3, 4))  --> 7
```

---

## 5. `rawget` 和 `rawset`

在元方法中，如果你要跳过 `__index/__newindex` 行为，可以用：

```lua
rawget(t, "key")    -- 直接获取，不触发 __index
rawset(t, "key", 1) -- 直接赋值，不触发 __newindex
```

---

## 6. 保护元表

```lua
local t = {}
setmetatable(t, {
  __metatable = "Access Denied"
})

print(getmetatable(t))  --> Access Denied
```

---
