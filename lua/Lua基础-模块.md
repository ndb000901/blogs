# Lua基础-模块

在 Lua 中，**模块（Module）** 是代码组织与封装的重要机制。通过模块，我们可以将功能进行分组、封装实现细节、避免命名冲突，并支持代码复用与分层结构。


一个模块本质上就是一个返回 table 的 Lua 文件。模块通常定义一组相关的函数、常量或变量，并通过 `return` 返回这个表，使其他代码可以通过 `require` 加载和使用它。

---

## 1.模块的定义

```lua
-- 文件名：mymodule.lua
local M = {}

function M.say_hello(name)
  print("Hello, " .. name)
end

function M.add(a, b)
  return a + b
end

return M
```


## 2. 模块的加载

使用 `require` 函数加载模块：

```lua
local my = require("mymodule")
my.say_hello("Lua")
print(my.add(1, 2))
```

### `require("模块名")` 背后的查找流程：

1. 查找是否已加载（`package.loaded["模块名"]`）。
2. 查找 Lua 文件（根据 `package.path`）。
3. 查找 C 模块（根据 `package.cpath`）。
4. 将模块结果保存在 `package.loaded["模块名"]` 中。

---

## 3. 模块路径与 `package.path`

查看当前 Lua 模块查找路径：

```lua
print(package.path)
```

路径类似：

```
./?.lua;/usr/local/share/lua/5.4/?.lua;/usr/local/share/lua/5.4/?/init.lua
```

其中：

- `?.lua`：匹配 `require("foo")` => `foo.lua`
- `?/init.lua`：匹配 `require("foo.bar")` => `foo/bar/init.lua`

也可以动态添加路径：

```lua
package.path = "./modules/?.lua;" .. package.path
```

---

## 4. 模块缓存机制

Lua 会缓存模块，避免重复加载：

```lua
local mod1 = require("mymodule")
local mod2 = require("mymodule")
print(mod1 == mod2)  --> true
```

### 如果需要重新加载模块：

```lua
package.loaded["mymodule"] = nil
local my = require("mymodule")
```

---

## 5. 模块名与文件名的关系

- `require("a.b")` => 查找 `a/b.lua` 或 `a/b/init.lua`
- `require("foo")` => 查找 `foo.lua` 或 `foo/init.lua`


## 6. 自定义模块加载器

Lua 中 `package.searchers` 决定了模块的查找机制，你可以自定义模块查找逻辑：

```lua
table.insert(package.searchers, 1, function(name)
  if name == "myvirtual" then
    return function()
      return { virtual = true }
    end
  end
end)

local v = require("myvirtual")
print(v.virtual)  --> true
```

---


