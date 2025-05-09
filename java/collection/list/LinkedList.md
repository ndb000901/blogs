# LinkedList

`LinkedList` 是 Java 中基于 **双向链表** 实现的集合类，实现了：

```java
public class LinkedList<E> extends AbstractSequentialList<E>
        implements List<E>, Deque<E>, Cloneable, Serializable
```

### 特点：

* 底层是 **双向链表**（Doubly Linked List）结构。
* 允许元素为 `null`。
* 插入和删除性能优于 `ArrayList`，但随机访问性能较差。
* 实现了 `Deque` 接口，可作为 **栈、队列、双端队列** 使用。

---

## 1. 底层结构（双向链表）

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

成员变量：

```java
transient int size = 0;
transient Node<E> first;
transient Node<E> last;
```

* 双向链表：每个节点有前后指针。
* `first` 指向头结点，`last` 指向尾结点。

---

## 2. 常用方法

### 2.1 `add(E e)`：尾部追加

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}

void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```

* 时间复杂度为 O(1)，比 `ArrayList` 快（无需扩容或拷贝）。

---

### 2.2 `get(int index)`：从头或尾遍历

```java
public E get(int index) {
    return node(index).item;
}

Node<E> node(int index) {
    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```

* 时间复杂度 O(n)，性能远低于 `ArrayList`。

---

### 2.3 `remove(int index)`：删除节点

```java
public E remove(int index) {
    return unlink(node(index));
}

E unlink(Node<E> x) {
    final E element = x.item;
    final Node<E> next = x.next;
    final Node<E> prev = x.prev;

    if (prev == null)
        first = next;
    else
        prev.next = next;

    if (next == null)
        last = prev;
    else
        next.prev = prev;

    x.item = null;
    x.next = null;
    x.prev = null;
    size--;
    modCount++;
    return element;
}
```

* 不涉及数组拷贝，删除效率比 `ArrayList` 更优。

---

### 2.4 `addFirst` / `addLast` / `removeFirst` / `removeLast`

这些方法源自 `Deque` 接口

```java
Deque<String> deque = new LinkedList<>();
deque.addFirst("A");   // 头插
deque.addLast("B");    // 尾插
deque.removeFirst();   // 移除头部
deque.removeLast();    // 移除尾部
```

* 全部是 O(1) 操作，适合用作 **栈、队列、双端队列**。

---

## 3.性能分析

| 操作类型 | ArrayList   | LinkedList |
| ---- |-------------| ---------- |
| 随机访问 | O(1)        | O(n)       |
| 尾部插入 | O(1)（触发扩容O(n)） | O(1)       |
| 中间插入 | O(n)        | O(n)       |
| 删除元素 | O(n)        | O(n)       |
| 头部插入 | O(n)        | O(1)       |

---

## 4. 线程安全性

* **非线程安全**。
* 可通过以下方式封装：

```java
List<String> syncList = Collections.synchronizedList(new LinkedList<>());
```

或者使用并发替代品：

* `ConcurrentLinkedDeque`：线程安全双端队列（基于非阻塞算法）。

---


