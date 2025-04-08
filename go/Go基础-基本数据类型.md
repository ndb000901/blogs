# Go基础-基本数据类型

## 1. 数值类型（Numeric Types）

### 整型（Integers）

| 类型        | 大小     | 说明                    |
|-------------|----------|-------------------------|
| `int`       | 与平台相关 | 通常是 32 或 64 位       |
| `int8`      | 8位       | -128 到 127             |
| `int16`     | 16位      | -32,768 到 32,767       |
| `int32`     | 32位      | -2^31 到 2^31-1         |
| `int64`     | 64位      | -2^63 到 2^63-1         |
| `uint`      | 无符号     | 与平台相关               |
| `uint8`     | 8位       | 0 到 255（等同 `byte`） |
| `uint16`    | 16位      | 0 到 65535              |
| `uint32`    | 32位      |                         |
| `uint64`    | 64位      |                         |
| `uintptr`   | 无符号     | 可保存指针的数值         |

> 一般推荐用 `int` 或 `uint`，除非你明确知道精度需求。

---

### 浮点型（Floats）

| 类型     | 大小   | 精度      |
|----------|--------|-----------|
| `float32`| 32位   | ~6位小数  |
| `float64`| 64位   | ~15位小数（默认推荐） |

```go
var f float64 = 3.1415
```

---

### 复数类型（Complex）

| 类型         | 描述                   |
|--------------|------------------------|
| `complex64`  | 实部虚部都是 `float32` |
| `complex128` | 实部虚部都是 `float64` |

```go
var c complex128 = complex(2.0, 3.0)
fmt.Println(real(c), imag(c)) // 输出实部和虚部
```

---

## 2. 字符类型（Characters）

### `byte`（uint8 的别名）

- 表示 **ASCII 字符** 或原始字节

### `rune`（int32 的别名）

- 表示一个 **Unicode 字符**

```go
var b byte = 'A'
var r rune = '中'
fmt.Println(b, r) // 65 20013
```

---

## 3. 字符串类型（String）

- 不可变的字节序列
- 支持 UTF-8 编码

```go
s := "Hello, 世界"
fmt.Println(len(s)) // 字节长度，不是字符数
```

可以使用 `[]byte(s)` 或 `[]rune(s)` 转换成字节数组或字符数组。

---

## 4. 布尔类型（Boolean）

```go
var ok bool = true
```

- 只能是 `true` 或 `false`
- 不支持 `1/0` 或 `非零为真`

---

## 5. 特殊类型

### `error`

- 内建接口类型，表示错误：

```go
var err error = errors.New("something went wrong")
```

### `nil`

- 表示指针、slice、map、chan、interface、function 的空值

---

## 6. 类型默认值（零值）

| 类型      | 零值       |
|-----------|------------|
| `int`     | 0          |
| `float64` | 0.0        |
| `bool`    | false      |
| `string`  | ""         |
| `pointer` | nil        |

--
