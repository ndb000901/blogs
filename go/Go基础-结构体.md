# Go基础-结构体

Go 语言的结构体（`struct`）是用户自定义的数据类型，它将多个不同类型的数据组合在一起，是 Go 实现面向对象编程风格的核心工具之一。

---

## 1. 结构体定义

```go
type Person struct {
    Name string
    Age  int
}
```

定义结构体时使用 `type` + `struct` 关键字。

---

## 2. 创建结构体实例

### 字面量方式（推荐）

```go
p := Person{
    Name: "Alice",
    Age:  30,
}
```

### 顺序赋值方式（不推荐）

```go
p := Person{"Bob", 25}
```

> 顺序赋值容易因为字段位置变化导致 bug，不建议。

---

## 3. 访问字段 & 修改字段

```go
fmt.Println(p.Name) // 访问字段
p.Age = 35          // 修改字段
```

---

## 4. 结构体指针

结构体也可以通过指针方式创建和访问：

```go
p := &Person{Name: "Tom", Age: 40}
fmt.Println(p.Name) // 自动解引用访问字段
```

---

## 5. 嵌套结构体

```go
type Address struct {
    City, Country string
}

type Employee struct {
    Name    string
    Address Address
}
```

访问方式：
```go
e := Employee{
    Name:    "John",
    Address: Address{"Shenzhen", "China"},
}
fmt.Println(e.Address.City)
```

---

## 6. 匿名嵌套（类似继承）

```go
type Animal struct {
    Name string
}

type Dog struct {
    Animal // 匿名字段，支持“继承”行为
    Breed  string
}

d := Dog{Animal: Animal{"Rover"}, Breed: "Husky"}
fmt.Println(d.Name) // 自动提升字段
```

---

## 7. 与 `new()` 创建结构体

```go
p := new(Person)
p.Name = "Lucy"
```

`new` 返回的是结构体指针（`*Person`），字段通过 `p.Name` 直接访问（自动解引用）。

---

## 8. 结构体比较（==）

结构体只要**所有字段都是可比较类型**，就可以使用 `==` 来比较：

```go
p1 := Person{"A", 10}
p2 := Person{"A", 10}
fmt.Println(p1 == p2) // true
```

---

## 9. 与 map、slice、channel 的结合

- 结构体切片：

```go
people := []Person{
    {"A", 20},
    {"B", 30},
}
```

- 结构体 map：

```go
m := map[string]Person{
    "a": {"Tom", 22},
}
```

---

## 10. 标签（Tag）与 JSON 序列化

结构体字段可以添加标签，用于序列化、校验等：

```go
type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}
```

配合 `encoding/json` 使用：

```go
json.Marshal(User{ID: 1, Name: "Jack"})
```

---

## 11. 空结构体（struct{}）

Go 的特殊结构体，**不占内存**，常用于信号传递等用途：

```go
ch := make(chan struct{})
go func() {
    // do something
    ch <- struct{}{}
}()
<-ch
```

---

