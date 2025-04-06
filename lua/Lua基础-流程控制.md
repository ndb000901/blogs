# Lua基础-流程控制


## 1. **条件判断：`if` 语句**

`if` 语句用于根据条件的真假来执行不同的代码块。Lua 提供了简单的 `if` 语句以及带有 `elseif` 和 `else` 的更复杂形式。

### 1.1 **单一条件 `if` 语句**

基本语法：

```lua
if condition then
    -- 代码块
end
```

- `condition` 是一个布尔值表达式，若为 `true`，则执行 `then` 后的代码块。

**示例：**

```lua
local x = 10
if x > 5 then
    print("x 大于 5")
end
```


### 1.2 **带 `else` 的 `if` 语句**

如果条件不成立，可以使用 `else` 语句来执行另一块代码。

```lua
if condition then
    -- 代码块 1
else
    -- 代码块 2
end
```

**示例：**

```lua
local x = 3
if x > 5 then
    print("x 大于 5")
else
    print("x 小于或等于 5")
end
```


### 1.3 **带 `elseif` 的 `if` 语句**

当有多个条件时，可以使用 `elseif` 语句来检查多个条件。

```lua
if condition1 then
    -- 代码块 1
elseif condition2 then
    -- 代码块 2
else
    -- 代码块 3
end
```

**示例：**

```lua
local x = 5
if x > 10 then
    print("x 大于 10")
elseif x == 5 then
    print("x 等于 5")
else
    print("x 小于 5")
end
```


