# Go基础-Map集合

在 Go 中，`map` 是一种内置的数据结构，用于存储键值对（key-value pair）。它类似于其他编程语言中的字典、哈希表、关联数组等。`map` 是 **无序** 的，这意味着遍历 `map` 时无法保证元素的顺序。

---

## 1. 基本语法

### 创建 `map`：

```go
// 创建一个空 map，键为 string，值为 int
m := make(map[string]int)

// 或者使用字面量创建并初始化
m := map[string]int{
    "a": 1,
    "b": 2,
}
```

---

## 2. 操作 `map`

### **插入元素**

```go
m := make(map[string]int)
m["a"] = 1  // 插入键值对 "a": 1
```

### **读取元素**

```go
val := m["a"]  // 读取键为 "a" 的值
```

如果键不存在，`val` 将是该值类型的零值：

```go
val := m["b"]  // 读取键为 "b"，没有则返回零值 (int 类型的零值是 0)
fmt.Println(val) // 输出 0
```

### **检查键是否存在**

通过第二个返回值判断键是否存在：

```go
val, ok := m["a"]
if ok {
    fmt.Println("Key found:", val)
} else {
    fmt.Println("Key not found")
}
```

`ok` 为 `true` 表示键存在，`false` 表示键不存在。

### **删除元素**

使用 `delete` 删除键值对：

```go
delete(m, "a")  // 删除键为 "a" 的元素
```

如果删除的键不存在，`delete` 不会报错。

---

## 3. `map` 的遍历

Go 提供了 `range` 来遍历 `map`，每次遍历返回 `key` 和 `value`：

```go
m := map[string]int{
    "a": 1,
    "b": 2,
    "c": 3,
}

for key, value := range m {
    fmt.Println(key, value)
}
```

> **无序性**：由于 `map` 内部是无序的，遍历 `map` 时输出的顺序每次都可能不同。

---

## 4. 注意事项

### **`map` 是引用类型**

`map` 是引用类型，这意味着当你将一个 `map` 赋值给另一个变量时，它们共享底层数据结构。修改其中一个 `map` 会影响另一个。

```go
m1 := make(map[string]int)
m1["a"] = 1
m2 := m1  // 共享同一底层数据
m2["b"] = 2

fmt.Println(m1) // 输出: map[a:1 b:2]
fmt.Println(m2) // 输出: map[a:1 b:2]
```

### **不能用 `map` 作为数组或切片的元素**

由于 `map` 是引用类型，因此它不能作为数组或切片的元素，因为它会导致程序崩溃。

```go
// 错误示范：不能使用 map 作为数组或切片元素
var arr [2]map[string]int // 错误
```

### **初始化 `map`**

如果尝试直接使用未初始化的 `map`，会导致运行时错误。必须使用 `make` 或字面量来初始化。

```go
var m map[string]int
m["a"] = 1 // 错误：m 没有初始化

// 正确做法：
m = make(map[string]int)
m["a"] = 1
```

---

## 5. 性能

`map` 在插入、删除和查找操作上非常高效，其平均时间复杂度为 **O(1)**，但在以下情况下会变得较慢：

1. **哈希冲突**：多个键值哈希到同一位置时，会导致冲突，需要额外的开销。
2. **重新哈希**：当 `map` 扩容时，所有键值对需要重新哈希到新位置。

`map` 内部的实现机制是基于哈希表，并采用拉链法来解决哈希冲突。

---

## 6. `map` 和并发

`map` 不是并发安全的，在多个 goroutine 同时读取或写入时，需要手动加锁。可以使用 `sync.Mutex` 或 `sync.RWMutex` 来确保并发安全。

```go
import "sync"

var m = make(map[string]int)
var mu sync.Mutex

func safeUpdate(key string, value int) {
    mu.Lock()
    m[key] = value
    mu.Unlock()
}
```


