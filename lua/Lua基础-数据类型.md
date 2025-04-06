# Lua基础-数据类型


| 数据类型     | 描述                                                                 |
|--------------|----------------------------------------------------------------------|
| `nil`        | 表示没有值或空值。                                                    |
| `boolean`    | 布尔类型，`true` 和 `false`。                                           |
| `number`     | 数字，表示整数或浮点数（Lua 使用双精度浮点数）。                        |
| `string`     | 字符串，表示文本，可以用单引号、双引号或长字符串定义。                |
| `table`      | 表，用于表示数组、字典等复杂数据结构。                                |
| `function`   | 函数，Lua 是函数式语言，函数可以赋值、传递和返回。                    |
| `userdata`   | 用户数据，通常用于 Lua 与 C 交互。                                     |
| `thread`     | 协程类型，用于实现协作式多任务处理。                                   |
| `lightuserdata` | 轻量级用户数据，表示 C 数据结构的指针。                              |


## 1. **nil**
- **描述**：`nil` 是 Lua 中的一个特殊类型，表示“没有值”或“空值”。
- **用途**：常用于表示变量没有初始化，或者函数没有返回值。
  
  ```lua
  local a = nil  -- a 没有值
  print(a)       -- 输出 nil
  ```

- **`nil` 也是空表的值**：
  ```lua
  local t = {}
  t[1] = nil  -- 删除索引 1 的元素
  ```

---

## 2. **Boolean**
- **描述**：布尔类型表示逻辑值，只有两个值：`true` 和 `false`。
  
  ```lua
  local isTrue = true
  local isFalse = false
  ```

- **注意**：
  - 任何值都可以用于条件判断，但只有 `nil` 和 `false` 被认为是 **假**，其他所有值都被认为是 **真**。

---

## 3. **Number**
- **描述**：Lua 的 `number` 类型用于表示数值。Lua 使用双精度浮点数表示所有数字（根据 IEEE 754 标准）。
- **整数和浮点数**：在 Lua 中，数字没有整数类型，所有的数字都是浮点数。

  ```lua
  local intNum = 42        -- 整数
  local floatNum = 3.14    -- 浮点数
  print(intNum + floatNum) -- 输出 45.14
  ```

---

## 4. **String**
- **描述**：字符串是用于表示文本的数据类型。Lua 中的字符串是不可变的，且可以通过单引号、双引号或长字符串（`[[ ]]`）来定义。

  ```lua
  local str1 = "Hello, World"
  local str2 = 'Lua is great!'
  
  -- 长字符串
  local longStr = [[
    This is a multi-line string.
    You can write across multiple lines.
  ]]
  ```

- **字符串操作**：
  - 连接字符串：`..`
  
    ```lua
    local greeting = "Hello" .. " " .. "Lua"
    print(greeting)  -- 输出 Hello Lua
    ```
  
  - 字符串长度：`#`
  
    ```lua
    print(#greeting)  -- 输出 8（"Hello Lua" 的长度）
    ```

---

## 5. **Table**
- **描述**：表（Table）是 Lua 中最重要的数据类型，它可以用来表示数组、字典、集合等数据结构。Lua 中几乎所有的复杂数据结构都是由表来实现的。

  ```lua
  local t = {key = "value", name = "Lua", version = 5.4}
  print(t.name)   -- 输出 Lua
  ```

- **注意**：
  - Lua 中的数组实际上是表，索引从 `1` 开始（不像大部分语言的数组从 `0` 开始）。
  - 表也可以是无序的，表现为字典（hashmap）形式。

  ```lua
  local arr = {1, 2, 3, 4}
  print(arr[1])  -- 输出 1
  ```

  - 空表：
  
    ```lua
    local emptyTable = {}
    ```

  - 表是 **引用类型**：修改一个表的值会影响所有引用它的变量。

---

## 6. **Function**
- **描述**：函数在 Lua 中是一级对象，可以赋值给变量、作为参数传递或作为返回值。
  
  ```lua
  local function add(a, b)
    return a + b
  end
  
  print(add(3, 4))  -- 输出 7
  ```

- **注意**：
  - 函数可以作为其他函数的返回值。

    ```lua
    local function outer()
      return function() return "Inner function" end
    end
    local inner = outer()
    print(inner())  -- 输出 Inner function
    ```

---

## 7. **Userdata**
- **描述**：`userdata` 是一种允许 Lua 与外部 C 库交互的数据类型。它表示一些内存区域，由外部代码管理，可以通过 Lua 的 `metamethod` 实现对象封装。
  
  ```lua
  -- Userdata 通常由 C 函数创建并由 Lua 使用
  ```

- **用途**：Lua 中的 `userdata` 类型通常用于封装 C 代码中的对象，允许 Lua 脚本访问外部资源（如数据库连接、文件描述符等）。

---

## 8. **Thread**
- **描述**：Lua 的 `thread` 类型用于支持协程（Coroutine）。协程允许你暂停函数的执行，稍后再恢复执行，常用于异步编程和并发控制。

  ```lua
  local co = coroutine.create(function()
    print("Hello from coroutine!")
  end)
  coroutine.resume(co)  -- 输出 Hello from coroutine!
  ```

- **注意**：
  - Lua 的 `thread` 主要用于协作式多任务处理，而不是操作系统线程。

---

## 9. **Lightuserdata**
- **描述**：`lightuserdata` 是指向 C 中数据的指针。它与 `userdata` 类似，但是是 **轻量级的**，通常用于表示一个 C 数据结构的指针。

---

