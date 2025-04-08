# Go基础-指针


## 1. 指针

一个指针是**指向另一个变量的内存地址**。

```go
var a int = 100
var p *int = &a // p 是指向 int 的指针，存了 a 的地址
```

- `&a`：获取变量 `a` 的地址
- `*p`：通过指针 `p` 访问它所指向的值（也叫解引用）

---

## 2. 指针的基本操作

```go
var a int = 10
var p *int = &a // 获取地址
fmt.Println(*p) // 解引用，输出 10
*p = 20         // 修改值
fmt.Println(a)  // 输出 20
```

---

## 3. 指针作为函数参数（引用传递）

```go
func addOne(n *int) {
    *n = *n + 1
}

func main() {
    x := 5
    addOne(&x)
    fmt.Println(x) // 输出 6
}
```

传指针进去可以让函数**修改原变量的值**。

---

## 4. 指针与结构体

```go
type Person struct {
    Name string
}

func main() {
    p := &Person{Name: "Tom"}
    fmt.Println(p.Name) // 自动解引用，等价于 (*p).Name
}
```

结构体指针访问字段时，Go 会自动帮你解引用。

---

## 5. new 函数

```go
p := new(int)  // 分配一个 int 的地址，值为 0
*p = 42
fmt.Println(*p)
```

等价于：

```go
var i int
p := &i
```

适合创建零值变量。

---

## 6. 指针数组 vs 数组指针

```go
var a = [3]int{1, 2, 3}
var p1 *[3]int = &a         // 数组指针
var p2 [3]*int              // 指针数组
```

- `*[3]int`：是一个指向整个数组的指针
- `[3]*int`：是一个数组，数组里的每个元素是指针

---

## 7. nil 指针

```go
var p *int
fmt.Println(p == nil) // true
```

声明但未初始化的指针默认是 `nil`，不能解引用！

---

## 8. Go 不支持指针运算

Go 指针不能像 C/C++ 一样做 `p++` 等操作，出于安全考虑。

---

## 9. 指针接收器 vs 值接收器（方法）

```go
type Counter struct {
    N int
}

func (c Counter) Add() { c.N++ }       // 值接收器：不会影响原值
func (c *Counter) RealAdd() { c.N++ }  // 指针接收器：可以修改原值
```

如果希望**方法内修改结构体字段生效**，必须使用指针接收器。



