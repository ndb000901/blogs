# Lua基础-函数

在 Lua 中，**函数**是非常重要的概念，函数既是第一类值，也可以作为参数传递和作为返回值返回。Lua 的函数有灵活的定义方式、支持闭包、支持递归等特性。

## 1. **定义函数**

### 1.1 **使用 `function` 关键字定义函数**

在 Lua 中，可以使用 `function` 关键字来定义一个函数。

```lua

function function_name(param1, param2)
    -- 函数体
end
```

- `function_name`：函数名称
- `param1, param2`：函数的参数，可以是任意数量和类型的参数
- 函数体：包含了函数的实际执行代码

**示例：**

```lua
function greet(name)
    print("Hello, " .. name)
end

greet("Lua")  -- 调用函数
```


### 1.2 **匿名函数**

Lua 也支持定义匿名函数，即没有名称的函数，通常用于将函数作为参数传递或赋值给变量。

```lua

local greet = function(name)
    print("Hello, " .. name)
end

greet("Lua")  -- 调用匿名函数
```


---

## 2. **返回值**

### 2.1 **返回多个值**

函数可以返回多个值，使用逗号分隔多个返回值。返回值可以是任何数据类型，也可以是表达式。

```lua
function sum_and_diff(a, b)
    return a + b, a - b
end

local sum, diff = sum_and_diff(10, 5)
print("Sum: " .. sum)  -- 输出 Sum: 15
print("Diff: " .. diff)  -- 输出 Diff: 5
```

- Lua 支持返回多个值，并可以分别赋给多个变量。

### 2.2 **没有返回值**

如果没有显式的 `return` 语句，函数将默认返回 `nil`。

```lua
function no_return()
    -- 没有显式的 return 语句
end

local result = no_return()
print(result)  -- 输出 nil
```

---

## 3. **函数作为参数**

由于函数是第一类值，Lua 允许将函数作为参数传递给其他函数，这使得它能够支持高阶函数的功能。

```lua
function apply_function(f, x)
    return f(x)
end

function square(x)
    return x * x
end

print(apply_function(square, 5))  -- 输出 25
```

在上面的示例中，`apply_function` 函数接受一个函数 `f` 作为参数，并将 `x` 作为该函数的输入。

---

## 4. **闭包 (Closure)**

在 Lua 中，**闭包**是指一个函数可以捕获并记住其所在环境中的变量。即使外部函数已经执行完，闭包仍然可以访问和修改这些变量。

```lua
function outer()
    local x = 10
    return function()
        print(x)
    end
end

local closure = outer()  -- 这里返回的是一个闭包
closure()  -- 输出 10
```

在上面的示例中，`closure` 函数是从 `outer` 函数返回的，并且能够访问 `outer` 中的变量 `x`，即使 `outer` 函数已经执行完毕。

---

## 5. **递归函数**

递归函数是指函数直接或间接地调用自身。递归必须有一个退出条件，否则会导致栈溢出。

```lua
function factorial(n)
    if n == 0 then
        return 1
    else
        return n * factorial(n - 1)
    end
end

print(factorial(5))  -- 输出 120
```

在上面的示例中，`factorial` 函数是递归调用自身来计算阶乘，退出条件是 `n == 0`。

---

## 6. **局部和全局函数**

在 Lua 中，函数默认是全局的，但是我们可以通过使用 `local` 关键字将函数声明为局部函数。

### 6.1 **全局函数**

```lua
function greet(name)
    print("Hello, " .. name)
end
```

此时，`greet` 是一个全局函数，可以在整个脚本中访问。

### 6.2 **局部函数**

```lua
local function greet(name)
    print("Hello, " .. name)
end
```

局部函数只在其定义的作用域内可见。局部函数的优点是它不会污染全局命名空间。

---

## 7. **可变参数**

Lua 中的函数可以接收可变数量的参数。可以使用 `...` 来表示可变参数。

```lua
function sum(...)
    local s = 0
    for _, v in ipairs({...}) do
        s = s + v
    end
    return s
end

print(sum(1, 2, 3))  -- 输出 6
print(sum(10, 20))    -- 输出 30
```

在上面的示例中，`...` 接收可变数量的参数，并通过 `ipairs` 函数遍历这些参数。

---

## 8. **函数的作用域**

函数内部定义的变量在函数外部无法访问，属于局部变量。通过 `local` 定义的变量在其定义的代码块内有效。

```lua
function test()
    local a = 10  -- 局部变量
    print(a)  -- 可以访问
end

test()
print(a)  -- 报错：a 未定义
```

---


