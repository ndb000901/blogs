# Go基础-函数

## 1. 函数基本语法

```go
func 函数名(参数列表) 返回值列表 {
    // 函数体
}
```

### 示例：
```go
func sayHello(name string) string {
    return "Hello, " + name
}
```

---

## 2. 多个参数 & 多个返回值

```go
func addAndSub(a, b int) (int, int) {
    return a + b, a - b
}
```

调用时：
```go
sum, diff := addAndSub(10, 3)
```

---

## 3. 命名返回值（可选）

可以给返回值命名，这样函数内部可以直接通过变量名 `return`。

```go
func divide(a, b float64) (result float64, ok bool) {
    if b == 0 {
        ok = false
        return
    }
    result = a / b
    ok = true
    return
}
```

---

## 4. 可变参数（variadic function）

使用 `...` 声明接收多个参数：

```go
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}

sum(1, 2, 3, 4) // => 10
```

---

## 5. 函数作为值 / 参数 / 返回值

Go 函数是“一等公民”，可以赋值、传参、返回：

```go
func operate(a, b int, op func(int, int) int) int {
    return op(a, b)
}

func add(a, b int) int { return a + b }

result := operate(2, 3, add) // 5
```

---

## 6. 匿名函数（lambda）

```go
f := func(x int) int {
    return x * x
}
fmt.Println(f(3)) // 9
```

也可以定义后立即调用（IIFE）：
```go
result := func(x int) int {
    return x + 1
}(10)
```

---

## 7. 闭包（Closure）

闭包是“捕获了外部变量的函数”，可以实现记忆功能：

```go
func counter() func() int {
    i := 0
    return func() int {
        i++
        return i
    }
}

c := counter()
fmt.Println(c()) // 1
fmt.Println(c()) // 2
```

---

## 8. defer 与函数

`defer` 延迟执行，用于释放资源、日志、异常恢复等：

```go
func test() {
    defer fmt.Println("最后执行")
    fmt.Println("先执行")
}
// 输出：先执行 -> 最后执行
```

多个 `defer` 是 **栈** 结构，后定义的先执行。

---

## 9. 函数嵌套（可定义在函数内部）

```go
func outer() {
    inner := func() {
        fmt.Println("inner")
    }
    inner()
}
```

---

## 10. init() 与 main()

- `main()` 是程序入口。
- `init()` 是初始化函数，在 `main()` 前自动执行，且不能带参数/返回值：

```go
func init() {
    fmt.Println("初始化操作")
}
```

---

