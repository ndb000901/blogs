# Go基础-注释

## 1. Go 中的注释类型

### **单行注释（行注释）**

```go
// 这是一个单行注释
fmt.Println("Hello, world!") // 在代码末尾也可以写注释
```

- 使用 `//` 开头。
- 常用于说明某一行代码的用途或调试信息。

---

### **多行注释（块注释）**

```go
/*
这是一个多行注释，
可以用于大段文字说明。
*/
```

- 使用 `/* ... */` 包裹注释内容。
- 不支持嵌套（即不能再嵌入一对 `/* ... */`）。
