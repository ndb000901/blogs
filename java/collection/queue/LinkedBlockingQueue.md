# LinkedBlockingQueue

`LinkedBlockingQueue` 是一个基于链表的、**可选容量的阻塞队列**，用于**生产者-消费者**场景中，是 Java 中最常用的并发队列之一。它支持**FIFO（先进先出）** 顺序，是 `java.util.concurrent` 包的一部分。

---

## 1. 类定义

```java
public class LinkedBlockingQueue<E> extends AbstractQueue<E>
    implements BlockingQueue<E>, java.io.Serializable
```

---

## 2. 核心特性

| 特性     | 描述                          |
| ------ | --------------------------- |
| 底层结构   | 链表结构                        |
| 容量     | 可选上限，默认 `Integer.MAX_VALUE` |
| 线程安全   | 支持阻塞，采用**两把锁**实现读写分离        |
| FIFO顺序 | 按插入顺序排序，先进先出                |
| 应用场景   | 多线程数据缓冲、生产者-消费者模型、线程池工作队列等  |

---

## 3. 构造方法

```java
LinkedBlockingQueue()                  // 容量无限（Integer.MAX_VALUE）
LinkedBlockingQueue(int capacity)      // 指定容量上限
LinkedBlockingQueue(Collection<? extends E> c)  // 初始化时添加集合中的元素
```

---

## 4. 内部结构

```java
// 内部 Node 节点
static class Node<E> {
    E item;
    Node<E> next;
}
```

* 使用**链表**实现队列；
* 使用两个锁：`takeLock` 与 `putLock`，分别控制出队和入队操作；
* 使用 `AtomicInteger count` 记录当前元素数量；
* 使用两个 `Condition`：`notEmpty` 和 `notFull` 控制阻塞/唤醒。

---

## 5. 关键字段

```java
private final int capacity;
private final AtomicInteger count = new AtomicInteger();
transient Node<E> head;
private transient Node<E> last;
private final ReentrantLock takeLock = new ReentrantLock();
private final Condition notEmpty = takeLock.newCondition();
private final ReentrantLock putLock = new ReentrantLock();
private final Condition notFull = putLock.newCondition();
```

---

## 6. 主要方法

### 添加元素

| 方法                                        | 是否阻塞 | 说明          |
| ----------------------------------------- | - | ----------- |
| `add(E e)`                                | 否 | 若满则抛异常      |
| `offer(E e)`                              | 否 | 若满则返回 false |
| `offer(E e, long timeout, TimeUnit unit)` | 是 | 等待指定时间      |
| `put(E e)`                                | 是 | 若满则阻塞直到有空位  |

### 获取元素

| 方法                                  | 是否阻塞 | 说明                 |
| ----------------------------------- | - | ------------------ |
| `poll()`                            | 否 | 没有元素返回 null        |
| `poll(long timeout, TimeUnit unit)` | 是 | 等待超时返回 null        |
| `take()`                            | 是 | 若空则阻塞直到有元素         |
| `peek()`                            | 否 | 查看队头但不移除，若空返回 null |

---

## 7. 线程安全策略

* 使用两把锁（`putLock` 和 `takeLock`）使得**插入和删除操作可以并发执行**；
* `count` 使用原子类 `AtomicInteger` 保证元素数量的线程安全；
* `Condition` 条件变量用于阻塞和唤醒等待线程。

---

## 8. 示例代码

```java
BlockingQueue<String> queue = new LinkedBlockingQueue<>(2);

// 生产者线程
new Thread(() -> {
    try {
        queue.put("A");
        queue.put("B");
        queue.put("C"); // 阻塞直到消费者取走元素
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();

// 消费者线程
new Thread(() -> {
    try {
        Thread.sleep(2000);
        System.out.println(queue.take()); // A
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();
```

---

## 9. 与其他阻塞队列对比

| 队列类型                    | 底层结构 | 是否有界 | 是否公平 | 插入性能     | 特点         |
| ----------------------- | ---- | - |----| -------- | ---------- |
| `ArrayBlockingQueue`    | 数组   | 是 | 支持 | 较快（连续内存） | 固定大小、支持读写锁 |
| `LinkedBlockingQueue`   | 链表   | 可选 | 否  | 较慢（分散内存） | 默认无限容量     |
| `PriorityBlockingQueue` | 堆    | 否 | 否  | 按优先级排序   | 非 FIFO     |

---
