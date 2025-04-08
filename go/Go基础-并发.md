# Go基础-并发

Go 语言的并发是其最具特色的功能之一，Go 提供了内建的支持并发的机制，主要通过 **goroutines** 和 **channels** 来实现。并发编程的核心理念是让多个任务在同一时间段内交替进行，以提高效率，尤其在多核 CPU 上，Go 的并发模型能够有效利用多核资源。

## 1. **Goroutines**

Goroutine 是 Go 中的轻量级线程，Go 运行时负责调度和管理这些线程，允许程序以更高效的方式运行多个并发任务。创建一个 goroutine 只需要极少的内存和开销。


Goroutines 通过 `go` 关键字来创建：

```go
package main

import (
	"fmt"
	"time"
)

func printNumbers() {
	for i := 0; i < 5; i++ {
		fmt.Println(i)
		time.Sleep(1 * time.Second)
	}
}

func main() {
	go printNumbers()  // 创建一个 Goroutine 来执行 printNumbers 函数
	fmt.Println("Main function")
	time.Sleep(6 * time.Second)  // 给 Goroutine 执行时间
}
```

在这个例子中，`go printNumbers()` 创建了一个 Goroutine，它将在后台并发执行 `printNumbers` 函数，而 `main` 函数中的代码继续执行。注意，由于 `main` 函数没有等待 `printNumbers` 执行完成，程序会立即退出。因此，使用 `time.Sleep` 来保证 Goroutine 有足够的时间执行。

### Goroutine 的特点：

- **轻量级**：Goroutine 占用的内存非常小，通常是几 KB。
- **调度由 Go 运行时管理**：Go 运行时会将多个 Goroutine 映射到一个或多个操作系统线程上。
- **并发执行**：Goroutines 可以在多个 CPU 核心上并行执行。

## 2. **Channels**

Channel 是 Go 用来实现 goroutines 之间通信的机制。它类似于管道，数据可以从一个 goroutine 通过 channel 传递到另一个 goroutine。Go 的 channel 是类型安全的，也就是说，只有特定类型的数据可以通过 channel 传递。

### 创建 Channel

Go 使用 `make` 函数创建一个 channel：

```go
ch := make(chan int)
```

这里的 `chan int` 表示这个 channel 用来传递 `int` 类型的数据。

### 发送和接收数据

在 goroutine 中，数据可以通过 channel 发送和接收：

```go
package main

import (
	"fmt"
)

func sendData(ch chan int) {
	ch <- 42  // 发送数据到 channel
}

func main() {
	ch := make(chan int)

	go sendData(ch) // 启动一个 Goroutine

	result := <-ch  // 从 channel 接收数据
	fmt.Println("Received:", result)
}
```

在这个例子中，`sendData` 函数通过 channel 发送数据，`main` 函数接收数据。 `ch <- 42` 表示发送数据到 channel，`result := <-ch` 表示从 channel 接收数据。

### **缓冲区 Channel**

Go 支持缓冲区 channel，即可以设置缓冲区的大小，允许一定数量的数据在没有接收方的情况下被发送：

```go
ch := make(chan int, 2) // 创建一个缓冲区大小为 2 的 channel
ch <- 1
ch <- 2
// 不会阻塞，因为缓冲区有空间
```

### **关闭 Channel**

在某些情况下，发送方可能需要关闭 channel 来通知接收方没有更多的数据可供接收。关闭 channel 使用 `close()` 函数：

```go
ch := make(chan int)

go func() {
    for i := 0; i < 3; i++ {
        ch <- i
    }
    close(ch) // 关闭 channel，通知接收方没有更多的数据
}()

for num := range ch {
    fmt.Println(num) // 会迭代接收到的数据，直到 channel 关闭
}
```

## 3. **Select 语句**

`select` 语句用于同时等待多个 channel 操作，可以实现多路选择（multiplexing）。

#### 示例：使用 `select` 进行多个 channel 的选择

```go
package main

import (
	"fmt"
	"time"
)

func sender(ch chan<- string, msg string) {
	time.Sleep(2 * time.Second)
	ch <- msg
}

func main() {
	ch1 := make(chan string)
	ch2 := make(chan string)

	go sender(ch1, "Hello from ch1")
	go sender(ch2, "Hello from ch2")

	// 使用 select 等待多个 channel
	select {
	case msg1 := <-ch1:
		fmt.Println("Received:", msg1)
	case msg2 := <-ch2:
		fmt.Println("Received:", msg2)
	}
}
```

在上面的代码中，`select` 会等待 `ch1` 或 `ch2` 中的数据，接收到数据后，就会执行相应的 `case` 语句。

### **`select` 的特点**
- 如果多个 channel 同时可读，`select` 会随机选择一个进行处理。
- `select` 语句可以使用 `default` 分支，当没有任何 channel 准备好时，`default` 会被执行。

## 4. **WaitGroups（等待组）**

Go 提供了 `sync.WaitGroup` 来等待多个 goroutines 完成。`WaitGroup` 会等待一组 goroutine 执行完成后再继续执行。

### 示例：使用 `WaitGroup` 等待多个 Goroutine 完成

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func task(id int, wg *sync.WaitGroup) {
	defer wg.Done() // 完成时通知 WaitGroup
	fmt.Printf("Task %d started\n", id)
	time.Sleep(2 * time.Second)
	fmt.Printf("Task %d completed\n", id)
}

func main() {
	var wg sync.WaitGroup

	for i := 1; i <= 3; i++ {
		wg.Add(1) // 增加一个等待计数
		go task(i, &wg) // 启动 goroutine
	}

	wg.Wait() // 等待所有 goroutine 完成
	fmt.Println("All tasks completed")
}
```

在这个例子中，`wg.Add(1)` 增加了一个等待任务，`wg.Done()` 通知 `WaitGroup` 当前任务完成，`wg.Wait()` 等待所有任务完成。

## 5. **Mutex（互斥锁）**

Go 提供了 `sync.Mutex` 来实现互斥锁，用于控制对共享资源的访问。Mutex 确保同一时间只有一个 goroutine 可以访问共享数据。

### 示例：使用 Mutex 保护共享资源

```go
package main

import (
	"fmt"
	"sync"
)

var count int
var mu sync.Mutex

func increment() {
	mu.Lock() // 锁定
	count++
	mu.Unlock() // 解锁
}

func main() {
	var wg sync.WaitGroup

	for i := 0; i < 1000; i++ {
		wg.Add(1)
		go func() {
			defer wg.Done()
			increment()
		}()
	}

	wg.Wait()
	fmt.Println("Final count:", count)
}
```

在这个例子中，`mu.Lock()` 和 `mu.Unlock()` 确保了对 `count` 变量的访问是互斥的，避免了并发访问导致的数据竞争。


