# Lua基础-包

在 Lua 中，**包（package）** 是用于组织多个模块的一种结构化方式。
---

## 1. 包与模块的关系

- **模块**：单个文件，返回一个表。
- **包**：由多个模块组成的目录结构，形成层级命名空间（比如 `mylib.utils.math`）。

例如：
```
mylib/         <-- 这是包
├── init.lua   <-- 是 mylib 包的主入口
├── utils/
│   ├── init.lua
│   └── math.lua
└── net/
    └── http.lua
```

---

## 2. 组织包结构

Lua 支持如下两种方式组织包模块：

### 方法一：每层目录中含 `init.lua`

```lua
-- 文件：mylib/init.lua
local mylib = {}

mylib.utils = require("mylib.utils")  -- 加载 utils/init.lua
mylib.net = require("mylib.net")      -- 加载 net/init.lua

return mylib
```

```lua
-- 文件：mylib/utils/init.lua
local utils = {}
utils.hello = function() print("hello utils") end
return utils
```

```lua
-- 文件：mylib/net/init.lua
local net = {}
net.http = require("mylib.net.http")
return net
```

```lua
-- 文件：mylib/net/http.lua
local http = {}
http.get = function(url) print("GET " .. url) end
return http
```

### 方法二：没有 `init.lua`，直接返回子模块

```lua
local http = require("mylib.net.http")
http.get("https://example.com")
```

---

## 3. require 如何找到包结构

`require("mylib.net.http")` 会按照如下路径去查找：

1. 匹配 `package.path`：
   - `mylib/net/http.lua`
   - `mylib/net/http/init.lua`（若有）

2. 匹配 `package.cpath`（用于加载 C 模块）

> 所以你需要确保包路径已包含在 `package.path` 中：

```lua
package.path = "./?.lua;./?/init.lua;" .. package.path
```

---

## 4. 包的缓存机制

包也是通过 `require()` 加载的，因此 **每个模块（不论是否在包中）只会加载一次**，之后会被缓存进：

```lua
package.loaded["mylib.net.http"]
```

如果你需要重新加载模块（比如热更新）：

```lua
package.loaded["mylib.net.http"] = nil
require("mylib.net.http")
```

---

## 5. 使用包结构

```lua
-- main.lua
local mylib = require("mylib")
mylib.utils.hello()
mylib.net.http.get("http://www.lua.org")
```

前提是：

- 当前目录包含 `mylib` 目录；
- `mylib` 有 `init.lua`；
- `package.path` 包含 `./?.lua;./?/init.lua;...`。

---

