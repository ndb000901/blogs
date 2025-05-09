# ArrayDeque

`ArrayDeque` 是 Java 提供的一个基于 **可变数组** 的 **双端队列（Deque）** 实现，性能优于 `Stack` 和 `LinkedList`，推荐作为栈和队列的替代方案。

---

## 1. 类定义

```java
public class ArrayDeque<E> extends AbstractCollection<E>
    implements Deque<E>, Cloneable, Serializable
```

* 位于 `java.util` 包。
* 实现 `Deque` 接口，支持 **队头队尾插入/删除**。
* 基于 **循环数组（环形缓冲区）**，不是线程安全的。

---

## 2. 特点

| 特性            | 说明                    |
| ------------- | --------------------- |
| 支持双端操作        | 可在队头/队尾插入和删除          |
| 动态扩容          | 底层数组容量不足时自动扩容         |
| 非线程安全         | 多线程环境需自行同步            |
| 不允许 `null` 元素 | 插入 null 会抛出异常         |
| 栈/队列通用        | 可当作栈（LIFO）或队列（FIFO）使用 |
| 无容量限制         | 默认初始容量 16，按需扩展为 2 的幂  |

---

## 3. 底层实现原理

* 内部使用一个 **循环数组** 来保存元素。
* 使用 `head` 和 `tail` 两个索引指针来追踪队列头尾。
* 容量不足时以 **2 倍扩容**。
* 队列空时，`head == tail`；满时，通过扩容避免 `head == tail` 冲突。

---

## 4. 构造方法

```java
ArrayDeque()                          // 默认容量 16
ArrayDeque(int numElements)          // 指定初始容量
ArrayDeque(Collection<? extends E>)  // 从集合构造
```

---

## 5. 常用方法

| 类别  | 方法                                 | 说明              |
| --- | ---------------------------------- | --------------- |
| 添加  | `addFirst(e)` / `addLast(e)`       | 插入到头/尾          |
| 添加  | `offerFirst(e)` / `offerLast(e)`   | 插入头/尾（推荐）       |
| 删除  | `removeFirst()` / `removeLast()`   | 移除头/尾           |
| 删除  | `pollFirst()` / `pollLast()`       | 移除头/尾，空时返回 null |
| 查看  | `getFirst()` / `getLast()`         | 查看头/尾元素         |
| 查看  | `peekFirst()` / `peekLast()`       | 查看头/尾，空时返回 null |
| 栈操作 | `push(e)` / `pop()` / `peek()`     | 用作栈时的方法         |
| 其他  | `size()` / `clear()` / `isEmpty()` | 通用操作            |

---

## 6. 示例代码

```java
Deque<String> deque = new ArrayDeque<>();

// 队列操作（FIFO）
deque.offer("A");
deque.offer("B");
deque.offer("C");
System.out.println(deque.poll());  // A

// 栈操作（LIFO）
deque.push("X");
deque.push("Y");
System.out.println(deque.pop());   // Y
```

---

## 7. 与其他集合对比

| 与谁对比                    | 优势                       |
| ----------------------- | ------------------------ |
| `Stack`                 | 更快，无同步开销（Stack 内部方法是同步的） |
| `LinkedList`            | 内存占用更少，访问更快（数组 vs 链表）    |
| `ArrayList`             | 支持双端操作，不局限尾部插入删除         |
| `ConcurrentLinkedDeque` | `ArrayDeque` 更快，但非线程安全   |

---

