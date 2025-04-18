# Condition

`Condition` 是 Java 中提供的一种更灵活的线程间通信机制，属于 `java.util.concurrent.locks` 包，是配合 `Lock` 接口使用的。

它的作用类似于传统的 `Object.wait()` / `notify()` / `notifyAll()`，但功能更强大、语义更清晰。

---

## 1. Condition

- `Condition` 由 `Lock` 对象创建，不能直接 new。
- 可以通过 `await()` 让线程挂起，通过 `signal()` 或 `signalAll()` 唤醒。
- 它本质上是 **一个等待/通知机制的抽象对象**，支持多条件队列。

---

## 2. 基本用法

```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();

lock.lock();
try {
    while (!某个条件满足) {
        condition.await(); // 当前线程挂起，释放锁
    }

    // 执行逻辑

    condition.signal(); // 唤醒一个等待该 condition 的线程
} finally {
    lock.unlock();
}
```

---

## 3. 与 Object.wait/notify 的区别

| 特性                        | `Object.wait/notify`   | `Condition`               |
|-----------------------------|-------------------------|---------------------------|
| 所属类                     | Object                  | java.util.concurrent.locks |
| 配套使用                   | synchronized            | Lock                      |
| 是否支持多个条件队列       | 只支持一个             | 支持多个                 |
| 是否支持中断控制           | 不太灵活               | 更灵活（支持可中断和不可中断）|
| 精确唤醒                   | 不支持                 | 支持多个 Condition 精确唤醒 |

---

## 4. 多个条件队列的示例

举例：一个阻塞队列，生产者/消费者使用不同的 Condition。

```java
class MyBlockingQueue<T> {
    private final Queue<T> queue = new LinkedList<>();
    private final int capacity;
    private final Lock lock = new ReentrantLock();
    private final Condition notFull = lock.newCondition();
    private final Condition notEmpty = lock.newCondition();

    public MyBlockingQueue(int capacity) {
        this.capacity = capacity;
    }

    public void put(T item) throws InterruptedException {
        lock.lock();
        try {
            while (queue.size() == capacity) {
                notFull.await(); // 等待空间
            }
            queue.add(item);
            notEmpty.signal(); // 通知消费者
        } finally {
            lock.unlock();
        }
    }

    public T take() throws InterruptedException {
        lock.lock();
        try {
            while (queue.isEmpty()) {
                notEmpty.await(); // 等待数据
            }
            T item = queue.poll();
            notFull.signal(); // 通知生产者
            return item;
        } finally {
            lock.unlock();
        }
    }
}
```

---

## 5. Condition 常用方法

| 方法                         | 说明                                           |
|------------------------------|------------------------------------------------|
| `await()`                    | 当前线程进入等待状态，直到 signal() 或中断     |
| `awaitUninterruptibly()`     | 等待但不能响应中断                            |
| `await(long time, TimeUnit)` | 超时等待，返回是否被唤醒                      |
| `signal()`                   | 唤醒一个等待的线程                            |
| `signalAll()`                | 唤醒所有等待的线程                            |

---

## 6. 注意事项

1. **必须在 lock.lock() 后调用 await/signal，否则抛出 IllegalMonitorStateException**
2. await 会释放锁，唤醒后会自动尝试重新获得锁
3. 推荐使用 `while` 循环判断条件，而不是 `if`，防止**虚假唤醒**

---


