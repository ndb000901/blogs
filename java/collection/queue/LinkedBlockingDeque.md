# LinkedBlockingDeque

`LinkedBlockingDeque` 是 Java 并发包 `java.util.concurrent` 中的一个 **线程安全、双端阻塞队列**，它基于双向链表实现，既支持 FIFO（先进先出），也支持 LIFO（后进先出），提供了比 `LinkedBlockingQueue` 更丰富的双端操作能力。

---

## 1. 类定义

```java
public class LinkedBlockingDeque<E> extends AbstractQueue<E>
    implements BlockingDeque<E>, java.io.Serializable
```

* **实现接口：**

  * `BlockingDeque<E>`：双端阻塞队列接口
  * `Serializable`、`Collection`、`Deque`、`Queue`

---

## 2. 底层结构与线程安全

| 特性     | 描述                                                      |
| ------ | ------------------------------------------------------- |
| 底层结构   | **双向链表（Doubly Linked List）**                            |
| 队列容量   | 默认无限大（`Integer.MAX_VALUE`），可设定上限                        |
| 线程安全   | 使用一把全局锁（`ReentrantLock`）+ 两个条件变量（`notEmpty`, `notFull`） |
| 阻塞操作支持 | 支持队列满/空时阻塞等待操作                                          |
| 元素顺序   | 按插入顺序，可头插、尾插，FIFO 或 LIFO                                |

---

## 3. 核心字段

```java
transient Node<E> first;
transient Node<E> last;
final ReentrantLock lock = new ReentrantLock();
private final Condition notEmpty = lock.newCondition();
private final Condition notFull = lock.newCondition();
private final int capacity;
private int count;
```

* `Node<E>` 为双向节点，含有 `prev`, `next`, `item`
* 用一把 `ReentrantLock` 控制所有操作的同步
* `notEmpty` 和 `notFull` 控制阻塞/唤醒逻辑

---

## 4. 构造函数

```java
LinkedBlockingDeque();                  // 默认容量 Integer.MAX_VALUE
LinkedBlockingDeque(int capacity);      // 指定容量
LinkedBlockingDeque(Collection<? extends E> c); // 用集合初始化
```

---

## 5. 主要方法（带阻塞与非阻塞）

### 添加元素

| 方法名                            | 端 | 是否阻塞 | 说明        |
| ------------------------------ | - |------| --------- |
| `addFirst(e)`                  | 头 | 否    | 满抛异常      |
| `addLast(e)`                   | 尾 | 否    | 满抛异常      |
| `offerFirst(e)`                | 头 | 否    | 满返回 false |
| `offerLast(e)`                 | 尾 | 否    | 满返回 false |
| `putFirst(e)`                  | 头 | 是    | 满时阻塞      |
| `putLast(e)`                   | 尾 | 是    | 满时阻塞      |
| `offerFirst(e, timeout, unit)` | 头 | 是    | 超时等待      |
| `offerLast(e, timeout, unit)`  | 尾 | 是    | 超时等待      |

### 获取与移除元素

| 方法名           | 端 | 是否阻塞 | 说明       |
| ------------- | - |------| -------- |
| `pollFirst()` | 头 | 否    | 空返回 null |
| `pollLast()`  | 尾 | 否    | 空返回 null |
| `takeFirst()` | 头 | 是    | 空阻塞      |
| `takeLast()`  | 尾 | 是    | 空阻塞      |
| `peekFirst()` | 头 | 否    | 查看队头不移除  |
| `peekLast()`  | 尾 | 否    | 查看队尾不移除  |

### 其他方法

* `removeFirst()`, `removeLast()`: 抛异常
* `iterator()`: 从头到尾遍历
* `descendingIterator()`: 从尾到头遍历

---

## 6. 使用示例

```java
BlockingDeque<String> deque = new LinkedBlockingDeque<>(2);

deque.putFirst("A"); // 插入头
deque.putLast("B");  // 插入尾

System.out.println(deque.takeFirst()); // 输出 A
System.out.println(deque.takeLast());  // 输出 B
```

---


## 7. 与其他队列对比

| 队列                      | 是否双端 | 是否阻塞 | 底层结构  | 是否线程安全 |
| ----------------------- |------| ---- | ----- | ------ |
| `ArrayBlockingQueue`    | 否    | 是    | 数组    | 是      |
| `LinkedBlockingQueue`   | 否    | 是    | 单向链表  | 是      |
| `LinkedBlockingDeque`   | 是    | 是    | 双向链表  | 是      |
| `ConcurrentLinkedDeque` | 是    | 否    | 非阻塞链表 | 是      |

---

