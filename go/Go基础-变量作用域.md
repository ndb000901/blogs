# Go基础-变量作用域



| 作用域类型     | 说明                               |
|----------------|------------------------------------|
| 包作用域       | 在整个包中都可见                   |
| 函数作用域     | 仅在函数内部可见                   |
| 代码块作用域   | 在 `{}` 包围的代码块中有效         |
| 文件作用域（常见于常量/变量/函数） | 只在当前 `.go` 文件中生效（不导出） |

---

## 1. 包作用域（全局变量）

定义在函数外部的变量，在同一个包内都可以访问。

```go
package main

import "fmt"

var globalVar = "包作用域变量" // 全局变量

func main() {
    fmt.Println(globalVar) // OK
}

func otherFunc() {
    fmt.Println(globalVar) // 也可以访问
}
```

注意：如果变量名首字母小写，只能在当前包访问；如果首字母大写，可以被其他包导入访问。

---

## 2. 函数作用域

在函数内定义的变量只能在函数内访问，函数外不可见。

```go
func main() {
    var msg = "函数作用域变量"
    fmt.Println(msg)
}

// 下面这样会报错：undefined: msg
// func anotherFunc() {
//     fmt.Println(msg)
// }
```

---

## 3. 代码块作用域（局部）

变量的作用域限定在它所在的花括号 `{}` 中。

```go
func main() {
    if true {
        x := 10
        fmt.Println(x) // OK
    }
    // fmt.Println(x) // 报错：x undefined
}
```

包括 `if`、`for`、`switch`、`函数` 等代码块。

---

## 4. 局部变量遮蔽（变量遮蔽）

Go 允许在内部作用域中定义与外部同名变量，会“遮蔽”外层变量。

```go
var name = "外部"

func main() {
    name := "内部"
    fmt.Println(name) // 打印：内部
}
```

---

## 5. 文件作用域 & 包导出规则

- **文件作用域：** 如果你定义了变量、常量或函数，首字母小写，它只能在当前包中使用（不是“真正意义的文件作用域”，但行为类似）。
- **包导出规则：** 首字母大写的变量、函数、结构体是可导出的（Public），小写的是私有的（Private）。

```go
// main.go
package main

import "fmt"

var Exported = "我可以被别的包访问"     // 可导出
var notExported = "只能包内访问"        // 不可导出

func main() {
    fmt.Println(Exported)
}
```

---


