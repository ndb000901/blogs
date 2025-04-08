# Go基础-循环


## 1. 基本 `for` 循环（类似 C 语言）

```go
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
```

结构：
```go
for 初始化语句; 条件表达式; 递增语句 {
    // 循环体
}
```

说明：
- 初始化语句只执行一次
- 每次迭代前判断条件表达式
- 每次迭代后执行递增语句

---

## 2. 作为 `while` 循环使用

```go
i := 0
for i < 10 {
    fmt.Println(i)
    i++
}
```

结构：
```go
for 条件 {
    // ...
}
```

等同于其他语言的 `while`

---

## 3. 无限循环（死循环）

```go
for {
    fmt.Println("一直执行")
}
```

可以配合 `break` 实现退出：

```go
for {
    if someCondition {
        break
    }
}
```

---

## 4. `for-range` 遍历（推荐）

用于遍历数组、切片、map、字符串、通道等。

### 4.1 遍历切片或数组
```go
nums := []int{2, 4, 6}
for index, value := range nums {
    fmt.Println(index, value)
}
```

忽略索引：
```go
for _, value := range nums {
    fmt.Println(value)
}
```

---

### 4.2 遍历字符串（按 Unicode 字符）
```go
s := "你好Go"
for i, r := range s {
    fmt.Printf("index=%d, rune=%c\n", i, r)
}
```

---

### 4.3 遍历 map
```go
m := map[string]int{"a": 1, "b": 2}
for k, v := range m {
    fmt.Println(k, v)
}
```

---

### 4.4 遍历通道（channel）
```go
ch := make(chan int)
go func() {
    for i := 0; i < 5; i++ {
        ch <- i
    }
    close(ch)
}()

for v := range ch {
    fmt.Println(v)
}
```

---

## 5. 控制循环的关键字

| 关键字 | 作用                     |
|--------|--------------------------|
| `break` | 终止当前循环             |
| `continue` | 跳过本次循环，继续下次迭代 |
| `goto` | 跳转到指定标签（不推荐）  |

---


