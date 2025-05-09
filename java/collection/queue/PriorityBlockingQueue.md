# PriorityBlockingQueue

`PriorityBlockingQueue` 是 Java 并发包 (`java.util.concurrent`) 提供的一个 **支持优先级排序的无界阻塞队列**，常用于生产者-消费者模型中，尤其当元素需要根据优先级顺序出队时。

---

## 1. 类定义

```java
public class PriorityBlockingQueue<E> extends AbstractQueue<E>
    implements BlockingQueue<E>, java.io.Serializable
```

* 位于 `java.util.concurrent` 包。
* 是一个 **无界**、**线程安全**、**基于优先级堆（最小堆）** 的阻塞队列。
* 不保证元素的 FIFO 顺序，仅保证优先级顺序。

---

## 2. 主要特点

| 特性                                  | 说明                                      |
| ----------------------------------- | --------------------------------------- |
| 无界队列                                | 不设置容量限制，但底层数组会自动扩容                      |
| 基于堆                                 | 使用 **二叉小顶堆** 实现，出队时返回最小（最高优先级）元素        |
| 阻塞支持                                | 支持 `take()` 等待直到可取出元素                   |
| 线程安全                                | 内部使用 `ReentrantLock` + `Condition` 实现同步 |
| 不允许 null                            | 插入 null 会抛出异常                           |
| 元素需实现 `Comparable` 或提供 `Comparator` | 用于优先级比较                                 |

---

## 3. 构造方法

```java
PriorityBlockingQueue()                          // 默认初始容量11，自然排序
PriorityBlockingQueue(int initialCapacity)       // 指定初始容量
PriorityBlockingQueue(int initialCapacity, Comparator<? super E> comparator)
PriorityBlockingQueue(Collection<? extends E> c) // 初始化 + 批量插入
```

---

## 4. 常用方法

| 类别 | 方法                                  | 说明        |
| -- | ----------------------------------- | --------- |
| 插入 | `put(E e)` / `offer(E e)`           | 无阻塞，立即插入  |
| 获取 | `take()`                            | 阻塞直到可取    |
| 获取 | `poll()` / `poll(timeout, unit)`    | 立即/超时等待   |
| 查看 | `peek()`                            | 查看头元素，不移除 |
| 其他 | `size()` / `clear()` / `contains()` | 集合操作      |

> 插入永远不会阻塞（因为无界），但 `take()` 会阻塞直到有元素。

---

## 5. 示例代码

```java
class Task implements Comparable<Task> {
    int priority;
    String name;

    public Task(int priority, String name) {
        this.priority = priority;
        this.name = name;
    }

    @Override
    public int compareTo(Task o) {
        return Integer.compare(this.priority, o.priority); // 优先级小的先执行
    }

    public String toString() {
        return name + ": " + priority;
    }
}

PriorityBlockingQueue<Task> queue = new PriorityBlockingQueue<>();
queue.put(new Task(3, "Low"));
queue.put(new Task(1, "High"));
queue.put(new Task(2, "Medium"));

System.out.println(queue.take()); // High: 1
```

---

## 6. 底层原理

1. **存储结构**：数组形式的 **小顶堆**（默认初始容量为 11）。
2. **排序机制**：元素实现 `Comparable` 接口，或者传入 `Comparator`。
3. **并发控制**：

   * 使用 `ReentrantLock` 控制并发。
   * `notEmpty` 条件变量用于 `take()` 时等待队列非空。
4. **扩容机制**：

   * 动态扩容：扩容为原来的 1.5 倍（使用 `Arrays.copyOf()`）。

---


