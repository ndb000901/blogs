# NIO设计核心

## 1. 三大核心组件

### 1.1 **Channel（通道）**
- 类似双向管道，既可以读也可以写（不像传统流只能单向）。
- 支持异步非阻塞 I/O 操作。
- 常见实现：
    - `FileChannel`
    - `SocketChannel`
    - `ServerSocketChannel`
    - `DatagramChannel`

### 1.2 **Buffer（缓冲区）**
- NIO 所有读写都通过 `Buffer`。
- 常见：`ByteBuffer`, `CharBuffer`, `IntBuffer` 等。
- 使用顺序：
    1. 写数据到 Buffer：`put()`
    2. 调用 `flip()` 切换读模式
    3. 读取数据：`get()`
    4. 调用 `clear()` 或 `compact()` 继续写入

### 1.3 **Selector（选择器）**
- 用于**非阻塞 I/O 多路复用**：一个线程处理多个连接。
- 原理类似 Linux 下的 `select` / `epoll`。
- `Selector` 可监控多个 Channel 的事件（如 `READ`, `WRITE`, `CONNECT`）。

---

## 2. NIO 的设计思想

| 特性         | 说明                                                                 |
|--------------|----------------------------------------------------------------------|
| 非阻塞（Non-blocking） | IO 操作不会阻塞线程，适合高并发服务端开发                              |
| 事件驱动     | 基于事件通知机制（Selector），避免线程阻塞等待                             |
| 零拷贝（Zero Copy） | 如 `FileChannel.transferTo()`、`mmap()`，减少用户态与内核态的切换            |
| 直接缓冲区   | `ByteBuffer.allocateDirect()` 直接分配堆外内存，提高读写效率               |
| 多路复用     | 一个线程可以管理多个通道（Channel），不再为每个连接分配一个线程              |
| 可组合性     | Buffer/Channel/Selector 解耦，灵活组合                                   |

---

## 3. 传统 IO vs NIO 对比

| 特性        | IO（传统）                  | NIO（新）                       |
|-------------|-----------------------------|----------------------------------|
| 编程模型    | 面向流 Stream               | 面向缓冲区 Buffer                |
| 阻塞性      | 阻塞 I/O                    | 非阻塞 I/O                      |
| 线程模型    | 每连接一线程                | 多路复用：一个线程处理多个连接   |
| 性能        | 简单但低效（线程开销大）     | 高并发、高吞吐                   |
| 应用场景    | 文件读写、少量连接服务      | 高并发网络服务器（如 Netty）     |

---



