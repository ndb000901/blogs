# Go基础-异常处理

 在 Go 语言中，**异常处理**的机制与许多其他编程语言不同。
 Go 没有传统的 `try-catch` 异常处理机制，而是通过**错误值（error）** 来处理程序中的异常情况。


## 1. 基本概念

Go 中没有“异常”（Exception）这一概念，而是通过返回值中的 `error` 类型来表示错误。当一个函数可能出错时，它会返回一个 `error` 类型的值来指示是否发生错误。如果发生错误，调用方可以根据返回的错误值进行相应的处理。

### `error` 类型

Go 中的 `error` 类型实际上是一个接口类型，定义在 `builtin` 包中，表示一个错误信息：

```go
type error interface {
    Error() string
}
```

所有实现了 `Error()` 方法的类型都可以作为 `error` 类型使用，通常是字符串类型来存储错误信息。

## 2. 错误处理的基本模式

Go 中的错误处理通常是通过多返回值来实现，特别是对于可能失败的函数，通常会返回两个值：一个是正常的结果值，另一个是 `error` 类型的值。调用者需要检查 `error` 是否为 `nil` 来判断操作是否成功。

### 示例：基本错误处理

```go
package main

import (
	"fmt"
	"errors"
)

// 模拟一个可能返回错误的函数
func divide(a, b int) (int, error) {
	if b == 0 {
		// 返回一个错误
		return 0, errors.New("division by zero")
	}
	return a / b, nil
}

func main() {
	result, err := divide(10, 0)
	if err != nil {
		// 如果返回了错误，输出错误信息
		fmt.Println("Error:", err)
	} else {
		// 如果没有错误，输出结果
		fmt.Println("Result:", result)
	}
}
```

在上面的示例中，`divide` 函数返回两个值，第一个是计算结果，第二个是一个 `error`。调用者检查 `error` 是否为 `nil`，来判断是否存在错误。

## 3. 错误处理的最佳实践

###  **尽早返回**

Go 推荐在遇到错误时，尽早地返回，避免嵌套层次过深。这样代码更加简洁和清晰。

```go
func readFile(file string) (string, error) {
    content, err := os.ReadFile(file)
    if err != nil {
        return "", err // 错误立刻返回
    }
    return string(content), nil
}
```

### **包装错误信息**

有时在返回错误时，我们希望包含更多的上下文信息。Go 提供了 `fmt.Errorf` 方法，可以用来创建带有上下文信息的错误。

```go
package main

import (
	"fmt"
	"os"
)

func readFile(file string) (string, error) {
	content, err := os.ReadFile(file)
	if err != nil {
		// 包装错误并添加更多上下文信息
		return "", fmt.Errorf("failed to read file %s: %w", file, err)
	}
	return string(content), nil
}

func main() {
	_, err := readFile("non_existent_file.txt")
	if err != nil {
		fmt.Println("Error:", err)
	}
}
```

在这个例子中，`fmt.Errorf` 函数使用 `%w` 语法包装了原始的错误。这样可以让错误保持链式结构，并能保留原始错误的信息。

## 4. 自定义错误类型

Go 允许定义自己的错误类型，从而为错误添加更多的上下文或特定的行为。这通常通过实现 `Error()` 方法来完成。

### 示例：自定义错误类型

```go
package main

import (
	"fmt"
)

// 定义一个自定义错误类型
type MyError struct {
	Op  string
	Msg string
}

func (e *MyError) Error() string {
	return fmt.Sprintf("Error in %s: %s", e.Op, e.Msg)
}

func riskyOperation() error {
	return &MyError{"riskyOperation", "something went wrong"}
}

func main() {
	err := riskyOperation()
	if err != nil {
		fmt.Println("Error:", err)
	}
}
```

在这个例子中，定义了一个 `MyError` 类型，并为其实现了 `Error()` 方法。可以创建一个带有更多信息的自定义错误类型。

## 5. 错误类型的判定与处理

可以使用 Go 的类型断言来检查错误的具体类型，或者使用 `errors.Is` 和 `errors.As` 来判断错误是否属于某个特定的类型或链。

### 示例：类型断言判断错误类型

```go
package main

import (
	"fmt"
	"errors"
)

// 自定义错误类型
type MyError struct {
	Msg string
}

func (e *MyError) Error() string {
	return e.Msg
}

func riskyFunction() error {
	return &MyError{"something bad happened"}
}

func main() {
	err := riskyFunction()
	if err != nil {
		// 类型断言检查错误是否为 MyError 类型
		if myErr, ok := err.(*MyError); ok {
			fmt.Println("MyError occurred:", myErr.Msg)
		} else {
			fmt.Println("Unknown error:", err)
		}
	}
}
```

## 6. Go 1.13 引入的 `errors.Is` 和 `errors.As`

在 Go 1.13 中，Go 引入了 `errors.Is` 和 `errors.As` 函数来处理错误链。通过这两个函数，可以更方便地判断错误是否属于某个特定的类型或是否包含某个错误。

### 示例：`errors.Is` 和 `errors.As`

```go
package main

import (
	"fmt"
	"errors"
)

var ErrNotFound = errors.New("not found")

func findItem() error {
	return ErrNotFound
}

func main() {
	err := findItem()
	if errors.Is(err, ErrNotFound) {
		fmt.Println("Item not found")
	} else {
		fmt.Println("Unknown error")
	}
}
```

## 7. `defer`, `panic`, 和 `recover`

Go 也提供了 `defer`、`panic` 和 `recover` 来处理异常情况。尽管 Go 中的错误处理通常是通过 `error` 来实现，但 `panic` 和 `recover` 用于处理更严重的错误，通常是在程序无法继续运行时使用。

### `panic` 和 `recover` 示例

```go
package main

import "fmt"

func causePanic() {
	panic("something went wrong")
}

func main() {
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("Recovered from panic:", r)
		}
	}()
	causePanic() // 调用会触发 panic
	fmt.Println("After panic") // 不会执行到这里
}
```

在这个例子中，`causePanic` 函数通过 `panic` 抛出异常，然后 `main` 函数通过 `defer` 和 `recover` 捕获并恢复了 panic。

---


