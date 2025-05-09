# ArrayBlockingQueue

`ArrayBlockingQueue` 是 Java 并发包 (`java.util.concurrent`) 中一个基于数组实现的 **有界阻塞队列**，用于线程间的数据交换，适合**固定容量**、**FIFO 顺序**、**高性能**的生产者-消费者场景。

---

## 1. 类定义

```java
public class ArrayBlockingQueue<E> extends AbstractQueue<E>
        implements BlockingQueue<E>, java.io.Serializable
```

* 位于 `java.util.concurrent` 包；
* 是一个**线程安全**、**有界（必须指定容量）** 的阻塞队列；
* **底层使用数组实现**；
* **先进先出（FIFO）** 顺序；
* 支持**阻塞插入/删除**操作。

---

## 2. 特性

| 特性       | 说明                                 |
| -------- | ---------------------------------- |
| 有界队列     | 构造时必须指定容量，不能动态扩容                   |
| 数组实现     | 元素存在数组中，通过循环数组（ring buffer）方式使用    |
| 线程安全     | 内部使用 `ReentrantLock` 和 `Condition` |
| FIFO 顺序  | 元素按入队顺序出队                          |
| 支持阻塞     | 满时 `put()` 阻塞，空时 `take()` 阻塞       |
| 不允许 null | 插入 null 会抛出 `NullPointerException` |

---

## 3. 构造方法

```java
ArrayBlockingQueue(int capacity)
ArrayBlockingQueue(int capacity, boolean fair)
ArrayBlockingQueue(int capacity, boolean fair, Collection<? extends E> c)
```

* `capacity`：**必须指定**的容量；
* `fair`：是否启用**公平锁**，默认 false（非公平，性能高）；
* `c`：初始数据，复制进队列。

---

## 4. 常用方法

### 插入元素

| 方法                                        | 阻塞性  | 说明                            |
| ----------------------------------------- |------| ----------------------------- |
| `offer(E e)`                              | 否    | 容量满返回 false                   |
| `offer(E e, long timeout, TimeUnit unit)` | 超时等待 | 超时等待                          |
| `put(E e)`                                | 是    | 阻塞直到可插入                       |
| `add(E e)`                                | 否    | 满则抛异常 `IllegalStateException` |

### 获取元素

| 方法                                  | 阻塞性  | 说明                    |
| ----------------------------------- |------| --------------------- |
| `poll()`                            | 否    | 队列为空返回 null           |
| `poll(long timeout, TimeUnit unit)` | 超时等待 | 超时等待                  |
| `take()`                            | 是    | 阻塞直到有元素               |
| `peek()`                            | 否    | 查看但不移除队首元素，队列空返回 null |

---

## 5. 核心字段与结构

```java
final Object[] items;          // 元素数组（定长）
int takeIndex;                 // 取出位置
int putIndex;                  // 插入位置
int count;                     // 当前元素个数

final ReentrantLock lock;      // 全局锁
final Condition notEmpty;      // 用于 take 阻塞等待
final Condition notFull;       // 用于 put 阻塞等待
```

* 使用循环数组（`takeIndex` 和 `putIndex` 自动 wrap-around）；
* 通过 `notEmpty.await()` / `notEmpty.signal()` 和 `notFull.await()` / `notFull.signal()` 实现阻塞控制。

---

## 6. 简单示例

```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(2);

// 生产者
new Thread(() -> {
    try {
        queue.put("A");
        queue.put("B");
        queue.put("C"); // 阻塞，直到有空位
    } catch (InterruptedException e) { }
}).start();

// 消费者
new Thread(() -> {
    try {
        Thread.sleep(2000);
        System.out.println(queue.take()); // A
    } catch (InterruptedException e) { }
}).start();
```

---

## 7. 底层原理（put 和 take）

### `put(E e)`

```java
public void put(E e) throws InterruptedException {
    lock.lock();
    try {
        while (count == items.length)
            notFull.await();     // 队列满时阻塞
        enqueue(e);
    } finally {
        lock.unlock();
    }
}
```

* 使用 `ReentrantLock`；
* 满队时调用 `notFull.await()` 进入等待；
* 插入元素后调用 `notEmpty.signal()` 唤醒消费者。

---

### `take()`

```java
public E take() throws InterruptedException {
    lock.lock();
    try {
        while (count == 0)
            notEmpty.await();   // 队列空时阻塞
        return dequeue();
    } finally {
        lock.unlock();
    }
}
```

* 空队时调用 `notEmpty.await()`；
* 取出元素后调用 `notFull.signal()` 唤醒生产者。

---

## 8. 使用建议与注意事项

| 项目          | 说明                          |
| ----------- | --------------------------- |
| 推荐场景        | 多线程生产者/消费者，固定容量需求           |
| 公平性         | 设置为 `true` 会降低吞吐量（避免线程“饿死”） |
| 无法动态扩容      | 容量需合理预估                     |
| 不支持 null 元素 | 否则抛出 `NullPointerException` |

---

## 9. 与其他阻塞队列对比

| 类名                      | 底层结构      | 是否有界  | 是否支持优先级 | 是否阻塞 | 特点                |
| ----------------------- | --------- | ----- | - | - | ----------------- |
| `ArrayBlockingQueue`    | 数组        | 是     | 否 | 是 | 内存局部性好，固定容量       |
| `LinkedBlockingQueue`   | 链表        | 是（默认容量很大） | 否 | 是 | 动态增长，吞吐量高         |
| `PriorityBlockingQueue` | 堆         | 否     | 是 | 是 | 根据优先级排序，不保证 FIFO  |
| `SynchronousQueue`      | 无缓冲       | 是（容量为 0） | 否 | 是 | 每次 put 必须有对应 take |
| `DelayQueue`            | 优先队列 + 延迟 | 否     | 是 | 是 | 适用于定时任务           |

---
