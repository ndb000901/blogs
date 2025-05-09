# Queue

在 Java 中，`Queue` 是一个接口，代表 **队列数据结构**。它是 **先进先出（FIFO）** 的典型结构，广泛用于任务调度、缓冲器等场景。



```java
public interface Queue<E> extends Collection<E>
```

Queue 接口主要方法分两组：

| 方法        | 抛异常         | 返回 null 或 false |
| --------- | ----------- | --------------- |
| 添加元素      | `add(e)`    | `offer(e)`      |
| 取队头元素但不删除 | `element()` | `peek()`        |
| 取并删除队头元素  | `remove()`  | `poll()`        |

---

## 1. 常用 Queue 实现类

| 实现类                     | 线程安全 | 特点                  |
| ----------------------- | ---- | ------------------- |
| `LinkedList`            | 否    | 双向链表，可做 Queue/Deque |
| `PriorityQueue`         | 否    | 最小堆实现，非 FIFO，按优先级出队 |
| `ArrayDeque`            | 否    | 基于循环数组，支持双端操作       |
| `ConcurrentLinkedQueue` | 是    | 非阻塞队列，基于 CAS        |
| `LinkedBlockingQueue`   | 是    | 有界阻塞队列，链表实现         |
| `ArrayBlockingQueue`    | 是    | 有界阻塞队列，数组实现         |
| `DelayQueue`            | 是    | 无界延迟队列，支持定时任务调度     |
| `SynchronousQueue`      | 是    | 无缓冲队列，生产者消费者一对一交换   |
| `PriorityBlockingQueue` | 是    | 支持优先级的阻塞队列          |

---

### 1.1 **LinkedList**

* 实现了 `Queue`、`Deque` 接口。
* 双向链表结构。
* 非线程安全。

### 1.2 **PriorityQueue**

* 元素按照自然顺序或自定义 `Comparator` 出队。
* 不是严格 FIFO。
* 基于最小堆。

### 1.3 **ArrayDeque**

* 基于循环数组的双端队列。
* 比 `LinkedList` 更高效。
* 不支持 null 元素。

---

### 1.4 **ConcurrentLinkedQueue**

* 基于链表，非阻塞，线程安全。
* 采用 CAS 实现。
* 适合高并发、无阻塞场景。

### 1.5 **LinkedBlockingQueue / ArrayBlockingQueue**

* 支持阻塞操作：`put()` / `take()`。
* `LinkedBlockingQueue`：无界或指定容量，链表实现。
* `ArrayBlockingQueue`：固定容量，数组实现。

### 1.6 **DelayQueue**

* 元素必须实现 `Delayed` 接口。
* 只有到期元素才能出队。
* 用于定时任务、缓存超时处理。

### 1.7 **SynchronousQueue**

* 无缓冲区：放入必须等待取出。
* 多用于线程之间传递数据。
* 类似“手递手”。

### 1.8 **PriorityBlockingQueue**

* 支持按优先级排序。
* 非 FIFO。
* 不阻塞空队列的迭代器（需注意）。

---
