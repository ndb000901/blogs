# ConcurrentLinkedQueue

`ConcurrentLinkedQueue` 是 Java 并发包（`java.util.concurrent`）中的一个**基于非阻塞算法的线程安全队列**，适用于**高并发环境下的“无界队列”**，实现了先进先出（FIFO）语义。

---

## 1. 类定义

```java
public class ConcurrentLinkedQueue<E> extends AbstractQueue<E>
    implements Queue<E>, Serializable
```

* **线程安全**：基于 **CAS（无锁）机制** 实现。
* **无界队列**：不会抛出容量满的异常，元素数量只受内存限制。
* **非阻塞**：不会阻塞线程（不像 `BlockingQueue`），插入和移除操作立即返回。

---

## 2. 底层数据结构

* 内部使用**单向链表**存储元素。
* 每个节点由内部类 `Node<E>` 表示，包含：

```java
static final class Node<E> {
    volatile E item;
    volatile Node<E> next;
}
```

* 头节点（head）和尾节点（tail）是两个 volatile 指针。
* 插入时尾插，删除时头删，保证 FIFO 顺序。

---

## 3. 核心方法

### 添加元素

```java
boolean offer(E e)
```

* 将元素添加到队尾，线程安全。
* 基于 **CAS + 自旋** 实现。

### 获取元素

```java
E poll()
```

* 弹出队头元素，若为空返回 `null`。
* 线程安全，CAS 实现，**不会阻塞**。

```java
E peek()
```

* 返回队头元素但不移除。

---

## 4. 示例代码

```java
Queue<String> queue = new ConcurrentLinkedQueue<>();

queue.offer("A");
queue.offer("B");

System.out.println(queue.peek()); // A
System.out.println(queue.poll()); // A
System.out.println(queue.poll()); // B
System.out.println(queue.poll()); // null
```

---


## 5. 优点与缺点

### 优点

* **无锁（非阻塞）**，避免线程切换，性能好。
* **线程安全**。
* **高吞吐**，在高并发场景表现优秀。

### 缺点

* **不支持阻塞**，适用于“尽量取，不等待”的场景；
* **非固定容量**，内存受限时需小心队列膨胀；
* 不适合精确控制线程等待（应使用 `BlockingQueue`）。

---

## 6. 与其他队列对比

| 队列类型                    | 是否线程安全 | 是否阻塞 | 底层结构 | 是否有界 |
| ----------------------- | - | - | ---- | -- |
| `ConcurrentLinkedQueue` | 是 | 否 | 单向链表 | 否  |
| `LinkedBlockingQueue`   | 是 | 是 | 链表   | 可配置 |
| `ArrayBlockingQueue`    | 是 | 是 | 数组   | 是  |
| `PriorityQueue`         | 否 | 否 | 堆    | 否  |
| `PriorityBlockingQueue` | 是 | 是 | 堆    | 否  |

---
