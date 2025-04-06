# Lua基础-命名


在 Lua 中，命名规则遵循一些基本的约定和规则，这些规则适用于变量、函数、表（table）字段等。下面是 Lua 命名的详解。

## 1. **命名规则**

### 1.1 标识符（Identifier）

标识符是用来命名变量、函数、表字段、局部变量等的名字。Lua 中标识符的规则如下：

- **合法字符**：
  - 标识符只能包含字母（大小写字母 `a-z`, `A-Z`）、数字（`0-9`）和下划线（`_`）。
  - 标识符不能以数字开头，必须以字母或下划线开头。
  - 标识符是 **区分大小写** 的（即 `var` 和 `Var` 被视为不同的标识符）。

- **关键字（Reserved words）**：
  - Lua 中有一组保留的关键字（如 `if`, `else`, `while`, `for` 等），不能用作变量名或函数名。常见的保留字有：
    - `and`, `break`, `do`, `else`, `elseif`, `end`, `false`, `for`, `function`, `if`, `in`, `local`, `nil`, `not`, `or`, `repeat`, `return`, `then`, `true`, `until`, `while`

  ```lua
  local function = 10  -- 错误，"function" 是一个保留字
  ```

---

## 2. **命名惯例**

### 2.1 变量命名

- **局部变量**：通常使用小写字母和下划线分隔（`snake_case` 风格）。例如：`local var_name = 10`。
  
  ```lua
  local counter = 0
  local total_value = 100
  ```

- **全局变量**：虽然 Lua 不强制区分全局和局部变量，但通常推荐将全局变量的命名与局部变量区分开。常见做法是使用大写字母（`UPPER_CASE` 风格）来表示全局常量或全局变量。
  
  ```lua
  GLOBAL_VAR = 100  -- 全局变量（不推荐常用，易出错）
  ```

- **常量**：虽然 Lua 中没有内建的常量支持，常见做法是用全大写字母来表示常量。
  
  ```lua
  MAX_LIMIT = 1000  -- 常量
  ```

### 2.2 函数命名

- **命名方式**：函数命名通常使用小写字母和下划线分隔（`snake_case` 风格），但也可以使用驼峰命名法（`camelCase`）。
  
  ```lua
  local function calculate_total()  -- snake_case
      return 100
  end
  
  local function calculateTotal()  -- camelCase
      return 100
  end
  ```

### 2.3 表（table）字段命名

表是 Lua 中最重要的数据结构，表字段的命名风格和变量类似，常用小写字母和下划线（`snake_case` 风格）。在对象式编程中，也可以使用驼峰式命名法（`camelCase` 风格）。

```lua
local person = {
  first_name = "John",
  last_name = "Doe",
  age = 30
}
```

---
