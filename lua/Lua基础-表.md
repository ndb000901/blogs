# Lua基础-表

在 Lua 中，**表（table）** 是一种非常核心的数据结构，几乎所有复杂的数据表示都依赖表来实现。表在 Lua 中既可以充当数组、字典、集合、对象、模块，甚至元表等

---

Lua 的表是一种**关联数组**，也就是**键值对（key-value）**的集合。键和值都可以是**任何类型**（除了 `nil` 作为键），非常灵活。

```lua
local t = {}  -- 创建一个空表
t["name"] = "Lua"
t[1] = 100
t[true] = "bool key"
```


## 1. 表的创建

### 1.1 空表

```lua
local t = {}
```

### 1.2 初始化表（数组风格）

```lua
local t = {10, 20, 30}  -- 等价于 { [1]=10, [2]=20, [3]=30 }
```

### 1.3 初始化表（键值对风格）

```lua
local t = {
  name = "Lua",
  age = 28,
  [100] = "a number key"
}
```

---

## 2. 访问和修改表元素

```lua
local t = {}
t["name"] = "Lua"
t[1] = 42

print(t["name"])   --> Lua
print(t[1])        --> 42

t["name"] = "Updated"
print(t["name"])   --> Updated
```

也可以使用语法糖：

```lua
t.name = "Lua"     -- 等价于 t["name"] = "Lua"
print(t.name)      -- 等价于 print(t["name"])
```

---

## 3. 遍历表

### 3.1 遍历数组部分（使用 `ipairs`）

```lua
local t = {10, 20, 30}
for i, v in ipairs(t) do
  print(i, v)
end
```

> `ipairs` 从索引 1 开始按顺序遍历

### 3.2 遍历所有键值（使用 `pairs`）

```lua
local t = {name = "Lua", age = 30, [1] = 100}
for k, v in pairs(t) do
  print(k, v)
end
```

---

## 4. 表的长度（`#` 运算符）

对**连续整数索引**的表可以使用 `#` 运算符来获取长度：

```lua
local t = {10, 20, 30}
print(#t)    --> 3
```

如果索引中断，`#` 可能行为不确定（因为 Lua 定义“长度”为某个连续段）。

---

## 5. 常用操作函数（`table` 库）

### `table.insert(t, value)`：末尾插入

```lua
local t = {1, 2}
table.insert(t, 3)   -- t = {1, 2, 3}
```

也可以指定插入位置：

```lua
table.insert(t, 2, 99)  -- t = {1, 99, 2, 3}
```

### `table.remove(t[, pos])`：移除元素

```lua
table.remove(t, 2)  -- 删除第2个元素，默认删除末尾元素
```

### `table.concat(t, sep)`：连接字符串数组

```lua
local t = {"Lua", "is", "great"}
print(table.concat(t, " "))  --> Lua is great
```

---

## 6. 表作为对象（面向对象风格）

Lua 中没有类（class）概念，但可以用表模拟：

```lua
local person = {
  name = "Tom",
  sayHi = function(self)
    print("Hi, I'm " .. self.name)
  end
}

person:sayHi()  -- 输出 Hi, I'm Tom
```

