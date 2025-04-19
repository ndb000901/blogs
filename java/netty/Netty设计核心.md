# Netty 设计核心

## 1. Netty 的设计哲学

> **Reactor 模式 + 可插拔 Pipeline + 高性能内存管理 + 异步事件驱动模型**

Netty 实现了一个强大的事件驱动框架，其设计目标是：**性能、灵活性、可靠性和可扩展性**。

---

## 2. Netty 核心架构图（概念图）
```
                ┌───────────────────────────┐
                │         用户业务逻辑        │
                └────────────┬──────────────┘
                             ↓
                ┌───────────────────────────┐
                │     ChannelPipeline       │← 责任链模式
                └────────────┬──────────────┘
                             ↓
                ┌───────────────────────────┐
                │  ChannelHandler (inbound/outbound) │
                └────────────┬──────────────┘
                             ↓
                ┌───────────────────────────┐
                │        Channel            │← 对SocketChannel封装
                └────────────┬──────────────┘
                             ↓
                ┌───────────────────────────┐
                │       EventLoop           │← 单线程事件循环
                └────────────┬──────────────┘
                             ↓
                ┌───────────────────────────┐
                │     Selector + Channel    │← Java NIO 底层
                └───────────────────────────┘
```

---

## 3. Netty 核心组件

### 3.1 **Channel**
- 抽象了 NIO 的 SocketChannel / ServerSocketChannel。
- 表示一个连接，如客户端的连接、服务器的监听通道。
- 所有 I/O 操作都由 Channel 发起，如 `read()`、`write()`、`bind()`、`connect()`。

### 3.2 **EventLoop**
- 每个 Channel 绑定一个 EventLoop（单线程执行器）。
- 是 Netty 的事件循环核心，负责：
    - I/O 事件监听（Selector）
    - 调用 ChannelHandler
    - 定时任务（ScheduledTask）

Netty 通过 EventLoopGroup 来管理多个 EventLoop 线程，实现高并发。

### 3.3 **ChannelPipeline & ChannelHandler**
- Pipeline 是一条**责任链**，由多个 Handler 组成。
- **InboundHandler**：处理读事件，如数据接收、解码。
- **OutboundHandler**：处理写事件，如编码、发送。
- 所有读写事件在 Pipeline 中按顺序流转，灵活插拔。

### 3.4 **ByteBuf**
- Netty 的内存缓冲区封装，性能远胜 NIO ByteBuffer。
- 特性：
    - 读/写指针分离（readerIndex / writerIndex）
    - 池化（PooledByteBufAllocator）
    - 零拷贝支持（slice(), duplicate(), compositeBuffer()）

### 3.5 **ChannelFuture**
- 所有操作都是**异步**的，返回 `ChannelFuture` 表示结果占位符。
- 通过 `addListener()` 注册回调，做异步编程。

---

## 4. Netty 的线程模型（主从 Reactor）

```
                 ┌────────────────────────────┐
                 │         BossGroup          │ 负责 accept
                 └───────────┬────────────────┘
                             ↓
                 ┌────────────────────────────┐
                 │         WorkerGroup        │ 负责 read/write
                 └───────────┬────────────────┘
                             ↓
                        EventLoop
                             ↓
                     ChannelPipeline
```

- **BossGroup**：监听端口、接收新连接（ServerSocketChannel）
- **WorkerGroup**：为每个连接分配线程，处理 I/O 操作（SocketChannel）
- 默认使用 `NioEventLoopGroup`，基于 NIO Selector 的非阻塞模型

---

## 5. I/O 流程（以 Server 为例）

1. `ServerBootstrap` 初始化并绑定端口
2. BossGroup 接收客户端连接，注册到 WorkerGroup
3. Worker 线程绑定 Channel + EventLoop
4. 客户端发来数据，触发 read 事件
5. 数据从 SocketChannel -> ByteBuf -> 解码器 -> ChannelHandler
6. 业务逻辑处理后，返回响应
7. Pipeline 中 OutboundHandler 编码并发送

---

## 6. 关键特性

| 特性               | 说明 |
|--------------------|------|
| 零拷贝              | 使用 Direct Buffer + 文件区域传输（`FileRegion`） |
| 高性能内存池         | ByteBuf 池化分配器，提升内存复用和分配效率 |
| 可插拔编解码器       | 自定义 `Decoder` / `Encoder` 组件 |
| 支持多种协议         | TCP、UDP、HTTP、WebSocket、Protobuf 等 |
| 定时任务/延时任务     | EventLoop 提供 `schedule` 支持 |
| 多种 Transport 支持   | NIO、EPoll（Linux）、KQueue（Mac） |

---

## 7.常用类清单

| 类名                  | 说明 |
|-----------------------|------|
| `ServerBootstrap`     | 启动服务器的引导类 |
| `Bootstrap`           | 启动客户端的引导类 |
| `ChannelInitializer`  | 初始化 Channel Pipeline |
| `ChannelInboundHandlerAdapter` | 自定义业务逻辑处理 |
| `ByteToMessageDecoder`| 解码器 |
| `MessageToByteEncoder`| 编码器 |
| `NioEventLoopGroup`   | 多线程事件循环组 |
| `DefaultChannelPipeline` | Pipeline 实现类 |

---


