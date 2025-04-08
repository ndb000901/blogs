# Go基础-流程控制


## 1. 条件判断：`if` 语句

### 基本语法：
```go
if condition {
    // 执行语句
}
```

### 示例：
```go
x := 10
if x > 5 {
    fmt.Println("x 大于 5")
}
```

---

### if-else 语句：
```go
if x < 0 {
    fmt.Println("负数")
} else {
    fmt.Println("非负数")
}
```

---

### if-else if-else 语句：
```go
if x < 0 {
    fmt.Println("负数")
} else if x == 0 {
    fmt.Println("零")
} else {
    fmt.Println("正数")
}
```

---

### 带初始化语句的 if：
```go
if n := len("hello"); n > 3 {
    fmt.Println("字符串长度大于3")
}
```

---

## 2. 分支语句：`switch`

### 基本语法：
```go
switch expression {
case val1:
    // ...
case val2:
    // ...
default:
    // ...
}
```

### 示例：
```go
score := 90
switch {
case score >= 90:
    fmt.Println("优秀")
case score >= 60:
    fmt.Println("及格")
default:
    fmt.Println("不及格")
}
```

### 多个 case 合并：
```go
switch day := 2; day {
case 1, 2, 3:
    fmt.Println("前半周")
case 4, 5:
    fmt.Println("后半周")
default:
    fmt.Println("周末")
}
```

### `fallthrough` 强制执行下一个 case（很少用）：
```go
switch x := 1; x {
case 1:
    fmt.Println("一")
    fallthrough
case 2:
    fmt.Println("二") // 会被执行
}
```

---

