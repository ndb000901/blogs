# Go基础-范围

在 Go 中，**范围（range）** 是用于遍历数组、切片、map、字符串和通道（channel）的一种语法形式。它可以同时返回“**索引（或键）**”和“**对应的值**”，是 Go 语言中处理集合的常见方式。

---

## 1. range 用法

```go
for index, value := range someCollection {
    // ...
}
```

- `index`：当前元素的下标 / key
- `value`：当前元素的值

---

## 2. range 适用于哪些数据类型？

### 数组 & 切片

```go
nums := []int{10, 20, 30}
for i, v := range nums {
    fmt.Println(i, v)
}
```

输出：

```
0 10
1 20
2 30
```

> `v` 是 **值的拷贝**，如果要修改原切片需用索引访问。

---

### 字符串（按 UTF-8 解码 rune）

```go
s := "Go语言"
for i, r := range s {
    fmt.Printf("index=%d, rune=%c\n", i, r)
}
```

输出：

```
index=0, rune=G
index=1, rune=o
index=2, rune=语
index=5, rune=言
```

> 注意：多字节字符的索引不是连续的，因为 UTF-8 是变长编码。

---

### map（键值对）

```go
m := map[string]int{"a": 1, "b": 2}
for k, v := range m {
    fmt.Println(k, v)
}
```

> map 是 **无序遍历**，每次执行可能顺序不同。

---

### channel（只返回值）

```go
ch := make(chan int, 2)
ch <- 100
ch <- 200
close(ch)

for v := range ch {
    fmt.Println(v)
}
```

> 必须 `close` 通道，否则 `range` 会死锁。

---

## 3. 省略变量用法

- 只要值，忽略索引：

```go
for _, v := range arr {
    fmt.Println(v)
}
```

- 只要索引：

```go
for i := range arr {
    fmt.Println(i)
}
```

---

## 4. range 注意事项

### **循环变量是复用的**

如果你在循环中使用 goroutine，闭包会“共享”同一个循环变量地址！

```go
for i, v := range arr {
    go func() {
        fmt.Println(i, v) // i、v 可能是最后一次迭代的值
    }()
}
```

正确方式：

```go
for i, v := range arr {
    i, v := i, v
    go func() {
        fmt.Println(i, v)
    }()
}
```

---

### **修改切片元素需要用索引**

```go
s := []int{1, 2, 3}
for i, v := range s {
    v *= 10 // 修改的是副本，不影响原切片
}
fmt.Println(s) // [1 2 3]
```

正确方式：

```go
for i := range s {
    s[i] *= 10
}
```

---

## 5. range 的底层原理简析（以切片为例）

`for i, v := range slice` 会被编译器翻译成：

```go
len := len(slice)
for i := 0; i < len; i++ {
    v := slice[i]
}
```

所以是性能非常高效的语法糖。

---

