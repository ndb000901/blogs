# Java IO 设计

`java.io` 包是 Java 中用于 **输入输出（I/O）操作** 的核心包，主要用于 **处理数据的读写**，包括文件、控制台、网络流等。它的设计体现了 **面向对象、装饰器模式（Decorator Pattern）** 以及 **流的分层封装思想**。

---

## 1. 核心设计理念

| 概念           | 说明                                                                 |
|----------------|----------------------------------------------------------------------|
| 面向抽象        | 所有输入输出类都继承自抽象类，如 `InputStream`、`Reader` 等           |
| 装饰器模式      | 支持在基本流上包裹多个功能增强流，如缓冲、数据、对象流                 |
| 单一职责        | 每个类只负责某一类 I/O 类型（字节、字符、数据、对象）                     |
| 层次分明        | 将 I/O 操作抽象为 4 层结构，方便扩展与功能组合                             |

---

## 2. 4 大核心抽象体系

### 2.1 字节流（处理二进制数据）
```java
InputStream    // 输入：byte
OutputStream   // 输出：byte
```
常见子类：
- FileInputStream / FileOutputStream
- BufferedInputStream / BufferedOutputStream
- DataInputStream / DataOutputStream

### 2.2 字符流（处理文本字符）
```java
Reader         // 输入：char
Writer         // 输出：char
```
常见子类：
- FileReader / FileWriter
- BufferedReader / BufferedWriter
- InputStreamReader / OutputStreamWriter（字节流转字符流）

### 2.3 缓冲流（提高效率）
通过内部缓冲数组，减少底层 I/O 操作次数：
```java
BufferedInputStream
BufferedReader
BufferedWriter
BufferedOutputStream
```

### 2.4 特殊功能流
- **DataXXXStream**：处理基本数据类型
- **ObjectXXXStream**：支持序列化对象
- **PushbackInputStream**：支持回退
- **PrintWriter / PrintStream**：支持格式化输出
- **PipedStream**：线程间通信

---

## 3. 装饰器模式的典型示例

```java
// 字节输入流
InputStream in = new FileInputStream("file.txt");

// 加缓冲提高效率
BufferedInputStream bufferedIn = new BufferedInputStream(in);

// 读取数据
int b = bufferedIn.read();
```

说明：
- `FileInputStream` 是基本的底层流
- `BufferedInputStream` 是装饰器（增强缓冲功能）
- `read()` 实际由缓冲层读取数据，提高性能

---

## 4. 字节流 vs 字符流

| 特性         | 字节流 (`InputStream`) | 字符流 (`Reader`) |
|--------------|------------------------|-------------------|
| 单位         | 8位 byte                | 16位 char         |
| 用途         | 图片、视频、音频等     | 文本文件（支持编码） |
| 是否可转换   | 使用 `InputStreamReader` ||
| 主要用途     | 处理原始字节            | 处理文本、字符数据  |

---


## 5. 常见应用场景

| 场景               | 推荐类                                     |
|--------------------|---------------------------------------------|
| 读写文本文件        | FileReader / FileWriter + BufferedXXX       |
| 读写二进制文件      | FileInputStream / FileOutputStream          |
| 对象序列化/反序列化 | ObjectOutputStream / ObjectInputStream      |
| 格式化文本输出      | PrintWriter                                 |
| 线程间通信          | PipedInputStream / PipedOutputStream         |

---


## 6. 与 java.nio 的对比

| 特性         | `java.io`                   | `java.nio`                    |
|--------------|-----------------------------|-------------------------------|
| 数据模型     | 面向流（Stream）              | 面向缓冲区（Buffer）           |
| 阻塞模式     | 阻塞                          | 非阻塞（可选）                 |
| 性能         | 较低（适合小量 I/O）         | 高效（适合高并发、大文件）       |
| 是否支持选择器 |                             | （Selector 支持多路复用）      |

---

