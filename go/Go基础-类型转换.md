# Go基础-类型转换


在 Go 语言中，**类型转换**（type conversion）是将一个值从一种类型转换为另一种类型的过程。Go 支持显式类型转换，提供了简单、清晰的语法。与其他语言不同，Go 不支持隐式类型转换，所有的类型转换必须是显式的。

## 1. 类型转换的基本语法

类型转换的基本语法如下：

```go
T(v)
```

- `T` 是目标类型。
- `v` 是要转换的值。

## 2. 常见的类型转换用法

### 2.1 **基础数据类型之间的转换**

Go 支持不同基础数据类型之间的显式转换，例如：

#### 整型和浮点型的转换

```go
var i int = 42
var f float64 = float64(i)  // int 转为 float64
fmt.Println(f)  // 输出：42.0
```

#### 整型和字符串的转换

Go 不支持直接将整型与字符串进行加法运算，但可以将它们显式转换为对应的类型：

```go
var i int = 65
var s string = string(i)  // int 转为 string（ASCII 字符）
fmt.Println(s)  // 输出：A
```

#### 字符串转换为字节切片

```go
s := "hello"
b := []byte(s)  // 字符串转换为字节切片
fmt.Println(b)  // 输出：[104 101 108 108 111]
```

#### 字符串与 rune（字符）转换

```go
s := "abc"
r := []rune(s)  // 字符串转换为 rune 切片
fmt.Println(r)   // 输出：[97 98 99]，表示 Unicode 编码
```

---

### 2.2 **接口类型与其他类型的转换**

Go 中的接口类型转换用于将一个值从一个具体类型转换为接口类型，或者将接口类型转换为具体类型。需要注意的是，类型转换会导致 `panic`，如果转换不成功，应该进行检查。

#### 接口类型转换为具体类型

```go
var x interface{} = 42  // 存储一个具体类型的值

// 尝试转换为 int 类型
i, ok := x.(int)
if ok {
    fmt.Println(i)  // 输出：42
} else {
    fmt.Println("Conversion failed")
}
```

#### 接口类型转换失败时

```go
var x interface{} = 42

// 尝试转换为 string 类型
s, ok := x.(string)
if !ok {
    fmt.Println("Conversion failed")  // 输出：Conversion failed
}
```

> 注意：如果你尝试将一个接口转换为不匹配的类型，且没有使用 `ok` 来检查，会导致运行时 `panic`：

```go
var x interface{} = 42
s := x.(string)  // 运行时 panic：interface conversion: interface {} is int, not string
```

---

### 2.3 **指针类型的转换**

指针类型之间也可以进行显式的类型转换，前提是它们指向的底层类型一致。

```go
var p *int
var q *float64

// 错误的转换
q = (*float64)(p) // 编译错误：指针类型不匹配

// 正确的转换
var p2 *int
var q2 *int
q2 = p2  // 指针类型转换，只能指向相同类型的底层数据
```

> 在进行指针类型转换时，必须保证两个指针的底层类型相同，否则编译会报错。

---

### 2.4 **数组与切片的转换**

数组和切片是不同的类型，因此不能直接进行转换，但可以使用 `copy` 函数或创建新的切片来转换。

#### 数组转切片

```go
arr := [3]int{1, 2, 3}
slice := arr[:]  // 数组转换为切片
fmt.Println(slice) // 输出：[1 2 3]
```

#### 切片转数组

切片无法直接转换为数组，因为数组的大小是类型的一部分，因此需要手动转换。

```go
slice := []int{1, 2, 3}
// 无法直接转换切片为数组，必须先定义目标数组的大小
arr := [3]int{}
copy(arr[:], slice)
fmt.Println(arr) // 输出：[1 2 3]
```

---

## 3. 类型转换的常见错误

### 3.1 **无法转换不兼容的类型**

Go 不支持将不兼容的类型直接转换，如将字符串转换为整型是非法的。你必须通过转换规则显式进行转换。

```go
// 错误示范
var s string = "100"
var i int = int(s)  // 编译错误：无法将 string 转换为 int
```

### 3.2 **指针类型转换的误用**

指针类型转换时，必须保证底层类型一致，否则会导致编译错误或运行时错误。

```go
var p *int
var q *string

q = (*string)(p)  // 错误，指针类型不匹配
```

---

## 4. `interface{}` 与类型断言

`interface{}` 是 Go 的空接口类型，可以持有任何类型的值。在实际开发中，通常需要将 `interface{}` 类型的变量转换为具体类型，称为类型断言。

### 4.1 **类型断言**

```go
var i interface{} = 10
value := i.(int)  // 类型断言，将空接口转换为 int 类型
fmt.Println(value) // 输出：10
```

### 4.2 **类型断言检查**

```go
var i interface{} = "hello"
str, ok := i.(string)  // 类型断言检查
if ok {
    fmt.Println(str)  // 输出：hello
} else {
    fmt.Println("Assertion failed")
}
```

---


