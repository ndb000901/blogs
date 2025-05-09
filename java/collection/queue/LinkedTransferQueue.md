# LinkedTransferQueue

`LinkedTransferQueue` 是 Java 并发包 `java.util.concurrent` 中的一个**基于链表的无界阻塞队列**，它在功能上比其他队列（如 `LinkedBlockingQueue`）更强大，特别适用于 **生产者-消费者模型中的高性能任务传递**。

---

## 1. 类定义

```java
public class LinkedTransferQueue<E> extends AbstractQueue<E>
        implements TransferQueue<E>, java.io.Serializable
```

* 属于 **无界阻塞队列**；
* 实现了 `TransferQueue` 接口，这是 `BlockingQueue` 的子接口；
* 支持更丰富的同步机制，比如：**立即传递**、**等待消费者接收**、**阻塞或非阻塞地接收元素**。

---

## 2. 核心特性

| 特性                      | 描述                                |
| ----------------------- | --------------------------------- |
| FIFO 顺序                 | 元素以先进先出的顺序传递或存储                   |
| 无界队列                    | 队列没有容量限制，内存足够就能一直添加元素             |
| 支持 transfer()           | 若没有消费者等待，`transfer()` 会阻塞直到有消费者接收 |
| 支持 tryTransfer()        | 尝试传递元素，若没有消费者等待就返回 false          |
| 支持 hasWaitingConsumer() | 判断当前是否有等待的消费者线程                   |

---

## 3. 底层结构

底层采用**单向链表结构**，节点类型包含两类：

* **数据节点（Data Node）**：包含一个实际的元素；
* **请求节点（Request Node）**：表示一个等待接收元素的线程（消费者）；

队列中维护一个头指针和尾指针，用于高效的插入、匹配等操作。

---

## 4. 常用方法

### 4.1 `void transfer(E e)`

* 阻塞直到元素被**消费者线程取走**；
* 若已有消费者在等待，则立即传递；
* 否则当前线程会阻塞，直到有消费者来接收。

### 4.2 `boolean tryTransfer(E e)`

* **非阻塞**尝试传递；
* 若有消费者在等待，则传递成功返回 true；
* 否则返回 false，不会入队。

### 4.3 `boolean tryTransfer(E e, long timeout, TimeUnit unit)`

* 尝试传递，如果没有消费者，会等待指定时间；
* 超时还没有消费者，返回 false。

### 4.4 `void put(E e)` / `boolean offer(E e)`

* 将元素加入队尾；
* 和普通阻塞队列一样，**不会等待消费者**。

### 4.5 `E take()`

* 获取并移除队头元素；
* 若无元素，则阻塞直到有数据；

### 4.6 `E poll(long timeout, TimeUnit unit)`

* 带超时时间的 `take()`。

---


## 5. 示例代码

```java
LinkedTransferQueue<String> queue = new LinkedTransferQueue<>();

// 消费线程
new Thread(() -> {
    try {
        String item = queue.take();
        System.out.println("消费者收到: " + item);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();

// 生产线程
new Thread(() -> {
    try {
        queue.transfer("任务1");
        System.out.println("任务1已被消费");
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();
```

运行结果类似：

```
消费者收到: 任务1
任务1已被消费
```

---
