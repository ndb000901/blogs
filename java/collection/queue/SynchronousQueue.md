# SynchronousQueue

`SynchronousQueue` 是 Java 中一个非常特殊的阻塞队列，位于 `java.util.concurrent` 包，它**不存储任何元素**，每个 `put` 操作必须等待一个 `take` 操作，反之亦然。

---

## 1. 核心特性

| 特性          | 说明                                |
| ----------- | --------------------------------- |
| 无容量         | 队列容量为 0，不能缓存元素                    |
| 一对一传递       | 每个元素必须被另一个线程立即接收                  |
| 线程安全        | 内部使用 CAS + 自旋或 `ReentrantLock` 实现 |
| 高性能         | 用于线程之间的**直接交换数据**                 |
| 非 FIFO 默认实现 | 可设置为公平（FIFO）或非公平（LIFO）模式          |

---

## 2. 类定义

```java
public class SynchronousQueue<E> extends AbstractQueue<E>
        implements BlockingQueue<E>, java.io.Serializable
```

---


## 3. 构造方法

```java
SynchronousQueue()                   // 默认构造，非公平（LIFO）
SynchronousQueue(boolean fair)      // 指定公平性
```

* `fair = true`：使用 `TransferQueue`，FIFO（公平模式）
* `fair = false`：使用 `TransferStack`，LIFO（非公平，默认）

---

## 4. 主要方法

### 元素插入

| 方法                                        | 是否阻塞 | 说明                     |
| ----------------------------------------- | - | ---------------------- |
| `put(E e)`                                | 是 | 阻塞等待消费者取走元素            |
| `offer(E e)`                              | 否 | 如果没有线程等待获取，则立即返回 false |
| `offer(E e, long timeout, TimeUnit unit)` | 是 | 在指定时间内尝试插入成功           |

### 元素获取

| 方法                                  | 是否阻塞 | 说明               |
| ----------------------------------- | - | ---------------- |
| `take()`                            | 是 | 阻塞直到有线程插入元素      |
| `poll()`                            | 否 | 如果没有元素，立即返回 null |
| `poll(long timeout, TimeUnit unit)` | 是 | 超时等待取出元素         |

---

## 5. 使用示例

```java
BlockingQueue<String> queue = new SynchronousQueue<>();

// 生产者线程
new Thread(() -> {
    try {
        System.out.println("Put A");
        queue.put("A");  // 阻塞直到有消费者
        System.out.println("Put A done");
    } catch (InterruptedException e) { }
}).start();

// 消费者线程
new Thread(() -> {
    try {
        Thread.sleep(1000);
        System.out.println("Take: " + queue.take());
    } catch (InterruptedException e) { }
}).start();
```

输出示例：

```
Put A
Take: A
Put A done
```

---

## 6. 内部实现核心类

`SynchronousQueue` 通过内部接口 `Transferer` 实现两种不同的传输机制：

| 模式      | 实现类             | 特点                            |
| ------- | --------------- | ----------------------------- |
| 非公平（默认） | `TransferStack` | LIFO 栈结构，自旋 + CAS，高性能         |
| 公平      | `TransferQueue` | FIFO 队列结构，使用链表和 `LockSupport` |

### 核心方法：`transfer(E e, boolean timed, long nanos)`

* 若 `e != null`：是 `put` 操作；
* 若 `e == null`：是 `take` 操作；
* 若有匹配线程在等待，立即交换数据；
* 否则进入等待状态（自旋/阻塞）直到匹配线程出现。

---


## 7. 与其他队列对比

| 队列类型                    | 是否有界 | 底层结构 | 是否支持阻塞 | 特点        |
| ----------------------- | - | ---- | - | --------- |
| `ArrayBlockingQueue`    | 是 | 数组   | 是 | 固定容量，FIFO |
| `LinkedBlockingQueue`   | 是 | 链表   | 是 | 可伸缩，吞吐量高  |
| `PriorityBlockingQueue` | 否 | 堆    | 是 | 带优先级      |
| `DelayQueue`            | 否 | 优先队列 | 是 | 延时任务队列    |
| `SynchronousQueue`      | 容量0 | 栈/队列 | 是 | 零容量、直接交换  |

---
