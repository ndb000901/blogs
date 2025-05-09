# CopyOnWriteArraySet

`CopyOnWriteArraySet` 是 Java 并发包（`java.util.concurrent`）中的一个线程安全的 `Set` 实现，底层基于 `CopyOnWriteArrayList`。

```java
public class CopyOnWriteArraySet<E> extends AbstractSet<E>
        implements Set<E>, java.io.Serializable
```

### 特点：

* **线程安全**
* **读多写少场景优化**
* **无重复元素**
* **保持元素插入顺序**
* **底层基于 `CopyOnWriteArrayList`**

---

## 1. 底层结构

核心是内部维护一个 `CopyOnWriteArrayList<E>` 实例：

```java
private final CopyOnWriteArrayList<E> al;
```

所有的 `Set` 操作其实都是委托给这个 `CopyOnWriteArrayList` 来完成的。

---

## 2. 构造方法

```java
public CopyOnWriteArraySet() {
    al = new CopyOnWriteArrayList<>();
}

public CopyOnWriteArraySet(Collection<? extends E> c) {
    al = new CopyOnWriteArrayList<>();
    for (E e : c)
        add(e);
}
```

> 初始化时会通过 `add(e)` 去重，而不是简单 `addAll()`。

---

## 3. 核心方法实现

### 3.1 `add(E e)`

```java
public boolean add(E e) {
    return al.addIfAbsent(e);
}
```

底层调用：

```java
public boolean addIfAbsent(E e) {
    Object[] snapshot = getArray();
    return indexOf(e, snapshot, 0, snapshot.length) >= 0 ? false : addIfAbsent(e, snapshot);
}
```

> 添加元素前会先检查是否已存在，确保集合元素唯一。

---

### 3.2 `contains(Object o)`

```java
public boolean contains(Object o) {
    return al.contains(o);
}
```

### 3.3 `remove(Object o)`

```java
public boolean remove(Object o) {
    return al.remove(o);
}
```

### 3.4 `iterator()`

```java
public Iterator<E> iterator() {
    return al.iterator(); // 读取快照，不受修改影响
}
```

---

## 4. 核心机制：写时复制（Copy-On-Write）

每次**写操作（add/remove/set）**，都会复制一个新的数组，在其上进行修改，并用 `volatile` 变量更新引用。

```java
private volatile Object[] array;
```

### 优点：

* **读操作无需加锁**，可并发安全读取。
* **迭代器遍历的是快照，不会抛 ConcurrentModificationException**。

### 缺点：

* **写操作开销大**，复制数组成本高。
* **内存占用高**，频繁写入时会造成大量数组副本。

---


## 5. 性能

| 操作           | 时间复杂度 | 特性            |
| ------------ | ----- | ------------- |
| 添加（add）      | O(n)  | 需要判断重复 + 拷贝数组 |
| 查询（contains） | O(n)  | 顺序遍历          |
| 删除（remove）   | O(n)  | 查找 + 拷贝数组     |
| 迭代器          | O(n)  | 快照，不受写影响，线程安全 |

---


