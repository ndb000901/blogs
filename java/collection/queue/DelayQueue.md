# DelayQueue

`DelayQueue` 是 Java 并发包（`java.util.concurrent`）中的一个 **无界阻塞队列**，用于放置实现了 `Delayed` 接口的元素。**只有当元素到期后才能从队列中取出**，非常适合实现延时任务、缓存超时处理等。

---

## 1. 类定义

```java
public class DelayQueue<E extends Delayed> extends AbstractQueue<E>
        implements BlockingQueue<E>
```

* 要求队列中的元素必须实现 `Delayed` 接口。

---

## 2. `Delayed` 接口定义

```java
public interface Delayed extends Comparable<Delayed> {
    long getDelay(TimeUnit unit); // 剩余延时时间
}
```

* `getDelay()`：返回与当前时间的**差值**，小于等于0时说明“到期了”。
* `compareTo()`：决定队列中元素的排序，按到期时间排序。

---

## 3. 底层结构

| 特性   | 描述                                       |
| ---- | ---------------------------------------- |
| 底层实现 | 基于**优先级队列**（PriorityQueue）               |
| 元素顺序 | 按**延时剩余时间**升序排列（越早到期越先出队）                |
| 队列容量 | 无界                                       |
| 线程安全 | 使用独占锁（`ReentrantLock`）+ 条件变量 `available` |
| 阻塞支持 | 消费时，如果队头未到期，则会阻塞等待到期或被唤醒                 |

---

## 4. 主要方法

### 添加元素

```java
boolean offer(E e)       // 添加元素，不支持 null
```

* 元素必须实现 `Delayed` 接口。
* 插入元素后会重新排序。

### 获取元素（可能阻塞）

| 方法                                    | 是否阻塞 | 说明                   |
| ------------------------------------- | - | -------------------- |
| `E take()`                            | 是 | **阻塞直到**有元素并且已过期     |
| `E poll()`                            | 否 | 若无过期元素，返回 null       |
| `E poll(long timeout, TimeUnit unit)` | 是 | 最多等待 timeout 时间      |
| `E peek()`                            | 否 | 查看队头元素但不移除，即使没过期也可看到 |

---

## 5. 使用示例

### 自定义 `Delayed` 元素类

```java
public class MyDelayTask implements Delayed {
    private long expireTime;
    private String data;

    public MyDelayTask(String data, long delayTimeMillis) {
        this.data = data;
        this.expireTime = System.currentTimeMillis() + delayTimeMillis;
    }

    @Override
    public long getDelay(TimeUnit unit) {
        return unit.convert(expireTime - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
    }

    @Override
    public int compareTo(Delayed o) {
        return Long.compare(this.getDelay(TimeUnit.MILLISECONDS), o.getDelay(TimeUnit.MILLISECONDS));
    }

    public String getData() {
        return data;
    }
}
```

### 使用 `DelayQueue`

```java
DelayQueue<MyDelayTask> queue = new DelayQueue<>();

queue.put(new MyDelayTask("task1", 3000));
queue.put(new MyDelayTask("task2", 1000));

while (true) {
    MyDelayTask task = queue.take(); // 阻塞直到任务到期
    System.out.println("处理任务: " + task.getData());
}
```

---



## 6. 和其他队列的对比

| 队列                      | 是否阻塞 | 是否有序    | 是否支持延时 |
| ----------------------- | ---- | ------- | ---- |
| `LinkedBlockingQueue`   | 是    | FIFO    | 否    |
| `PriorityBlockingQueue` | 是    | 自定义优先级  | 否    |
| `DelayQueue`            | 是    | 按剩余延时时间 | 是    |


