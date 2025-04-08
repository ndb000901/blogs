# Go基础-接口

Go 语言中的 **接口（interface）** 是一种非常核心的特性，它允许我们以抽象的方式编写程序，实现 **多态、多实现、松耦合、可替换、可测试性好** 的设计风格。它和传统面向对象语言中的接口类似，但 Go 接口更简洁、灵活。

---

## 1. 接口定义

```go
type 接口名 interface {
    方法名1(参数列表) 返回值列表
    方法名2(参数列表) 返回值列表
}
```

### 示例：

```go
type Speaker interface {
    Speak() string
}
```

---

## 2. 实现接口（隐式实现）

**Go 接口不需要显式声明 `implements`，只要实现了接口中的方法即可。**

```go
type Person struct {
    Name string
}

func (p Person) Speak() string {
    return "Hello, I am " + p.Name
}
```

因为 `Person` 类型实现了 `Speak()` 方法，因此它自动实现了 `Speaker` 接口。

---

## 3. 使用接口（多态）

```go
func saySomething(s Speaker) {
    fmt.Println(s.Speak())
}

p := Person{"Alice"}
saySomething(p) // 多态调用
```

---

## 4. 多个类型实现同一接口

```go
type Dog struct{}

func (d Dog) Speak() string {
    return "Woof!"
}

type Cat struct{}

func (c Cat) Speak() string {
    return "Meow!"
}
```

```go
animals := []Speaker{Dog{}, Cat{}}
for _, a := range animals {
    fmt.Println(a.Speak())
}
```

---

## 5. 接口嵌套（组合）

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type ReadWriter interface {
    Reader
    Writer
}
```

---

## 6. 空接口 `interface{}`

空接口没有任何方法，**可以表示任意类型**，相当于 Java 的 `Object`。

```go
var x interface{}
x = 100       // int
x = "hello"   // string
x = Person{}  // 自定义类型
```

---

## 7. 类型断言（Type Assertion）

将接口还原成原来的具体类型：

```go
var i interface{} = "hello"
s := i.(string)        // 正确断言
s, ok := i.(string)    // 安全断言，ok 为 true/false
```

---

## 8. 类型选择（Type Switch）

```go
switch v := i.(type) {
case int:
    fmt.Println("int:", v)
case string:
    fmt.Println("string:", v)
default:
    fmt.Println("unknown type")
}
```

---

## 9. 接口与指针 vs 值接收器

### 值接收器实现接口：

```go
func (p Person) Speak() string {...}
```

- `Person` 实现了接口
- `*Person` 也能用（自动解引用）

### 指针接收器实现接口：

```go
func (p *Person) Speak() string {...}
```

- 只有 `*Person` 实现接口，`Person` 不行

---

## 10. nil 接口陷阱

```go
var p *Person = nil
var s Speaker = p
fmt.Println(s == nil) // false
```

因为接口底层是一个 `(type, value)` 对，即使 `value` 是 nil，但 `type` 不为 nil，所以整个接口不为 nil。

---

## 11. 接口的本质（interface 的内部结构）

接口底层可以理解为：

```go
type iface struct {
    tab  *itab
    data unsafe.Pointer
}
```

这是它“可以指向任意类型”的根本原因。

---

