# PriorityQueue

`PriorityQueue` 是 Java 中的一个 **基于优先级堆（最小堆）** 实现的队列，不遵循严格的 FIFO 顺序，而是按照元素的 **优先级顺序** 出队。

---

## 1. 类定义

```java
public class PriorityQueue<E> extends AbstractQueue<E>
    implements java.io.Serializable
```

* 属于 `java.util` 包。
* 实现了 `Queue` 接口。
* 默认是 **最小堆**：越小的元素优先级越高，先出队。

---

## 2. 底层数据结构

* 底层使用 **数组实现的二叉堆**。
* 默认是 **小顶堆**，通过数组结构维护堆性质。
* 元素必须是：

  * 可比较（`Comparable`），或
  * 提供了 `Comparator` 比较器。

---

## 3. 构造方法

```java
// 默认初始容量 11，使用自然顺序
PriorityQueue()

// 指定初始容量
PriorityQueue(int initialCapacity)

// 指定比较器
PriorityQueue(Comparator<? super E> comparator)

// 用集合初始化
PriorityQueue(Collection<? extends E> c)
```

---

## 4. 常用方法

| 方法                         | 说明              |
| -------------------------- | --------------- |
| `boolean offer(E e)`       | 添加元素，自动调整堆      |
| `E poll()`                 | 弹出优先级最高的元素（最小的） |
| `E peek()`                 | 查看堆顶元素，但不移除     |
| `boolean remove(Object o)` | 移除指定元素          |
| `int size()`               | 元素个数            |
| `boolean isEmpty()`        | 是否为空            |

---

## 5. 示例代码

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(5);
pq.offer(1);
pq.offer(3);

while (!pq.isEmpty()) {
    System.out.print(pq.poll() + " ");
}
// 输出：1 3 5
```

自定义优先级（大顶堆）：

```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
maxHeap.offer(10);
maxHeap.offer(30);
maxHeap.offer(20);
System.out.println(maxHeap.poll()); // 30
```

---

