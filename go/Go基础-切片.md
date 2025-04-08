# Go基础-切片

## 1. 切片

切片是对底层数组的一个**连续片段的引用**，包含三部分：

- **指针**：指向底层数组某处元素
- **长度**：slice 当前包含的元素数量
- **容量**：从切片起始位置到底层数组末尾的最大元素数

```go
s := []int{1, 2, 3}
```

此时底层数组是 `[1, 2, 3]`，`len(s)==3`，`cap(s)==3`

---

## 2. 切片的创建方式

### 基于数组切片：

```go
arr := [5]int{0, 1, 2, 3, 4}
s := arr[1:4]       // [1 2 3]，包含 1~3，不包括4
```

### 使用字面量：

```go
s := []string{"go", "rust", "c"}
```

### 使用 make 创建指定容量的切片：

```go
s := make([]int, 3, 5) // len=3, cap=5，默认值为 0
```

---

## 3. 切片长度与容量

```go
s := []int{1, 2, 3, 4, 5}
fmt.Println(len(s)) // 5
fmt.Println(cap(s)) // 5
```

```go
s2 := s[1:3] // [2 3]
fmt.Println(len(s2)) // 2
fmt.Println(cap(s2)) // 4（从 s[1] 到 s[4]）
```

---

## 4. 使用 append 添加元素

```go
s := []int{1, 2}
s = append(s, 3, 4)
fmt.Println(s) // [1 2 3 4]
```

容量不足时，**自动扩容（一般是 2 倍策略）**，分配新的底层数组。

---

## 5. 切片是引用类型

切片传递的是对底层数组的引用：

```go
func modify(s []int) {
	s[0] = 100
}

func main() {
	a := []int{1, 2, 3}
	modify(a)
	fmt.Println(a) // [100 2 3]，原切片被修改
}
```

但如果 `append` 后超出容量，新切片将指向新数组：

```go
func modify(s []int) {
	s = append(s, 4) // 容量不足时分配新数组
	s[0] = 100       // 修改的是新数组
}
```

---

## 6. 切片截取操作

```go
a := []int{0, 1, 2, 3, 4, 5}
s := a[1:4]    // [1 2 3]
s2 := s[:2]    // [1 2]
s3 := s[1:]    // [2 3]
```

也支持三索引切片语法控制容量：

```go
s := a[1:3:4] // len=2, cap=3（从 index 1 到 index 4）
```

---

## 7. 删除元素（手动实现）

Go 没有内建 `remove`，你可以这样做：

```go
a := []int{1, 2, 3, 4, 5}
i := 2 // 删除下标2的元素
a = append(a[:i], a[i+1:]...)
```

---

## 8. copy 函数

复制切片内容：

```go
src := []int{1, 2, 3}
dst := make([]int, len(src))
copy(dst, src) // 返回复制元素个数
```

---

## 9. nil 和空切片的区别

```go
var a []int       // nil 切片，len=0, cap=0
b := []int{}      // 空切片，len=0, cap=0
fmt.Println(a == nil) // true
fmt.Println(b == nil) // false
```

通常建议使用空切片，而非 nil（例如用于 JSON 编码）。

---




