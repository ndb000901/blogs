# Vector

`Vector` 是 Java 中最早的动态数组类之一，存在于 JDK 1.0，位于 `java.util` 包下：

```java
public class Vector<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, Serializable
```

### 特性概览：

* **底层数据结构为数组**
* 支持动态扩容（类似 `ArrayList`）
* **线程安全（方法加了 synchronized）**
* 查询效率高、增删效率较低
* 属于 **“同步版 ArrayList”**，但现在已不推荐新项目使用

---

## 1. 底层结构

```java
protected Object[] elementData;
protected int elementCount;
protected int capacityIncrement;
```

* `elementData`：数据存储的数组
* `elementCount`：当前存储的元素数量（size）
* `capacityIncrement`：扩容时每次增加的容量（可为0，表示翻倍）

---

## 2. 构造方法

```java
// 默认构造，容量为 10
public Vector() {
    this(10);
}

// 指定初始容量
public Vector(int initialCapacity) {
    this(initialCapacity, 0);
}

// 指定初始容量和扩容增量
public Vector(int initialCapacity, int capacityIncrement) {
    super();
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
    this.elementData = new Object[initialCapacity];
    this.capacityIncrement = capacityIncrement;
}
```

---

## 3. 核心方法

### 3.1 `add(E e)` - 加入元素（线程安全）

```java
public synchronized boolean add(E e) {
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}
```

内部扩容逻辑：

```java
private void ensureCapacityHelper(int minCapacity) {
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = (capacityIncrement > 0) ?
        (oldCapacity + capacityIncrement) : (oldCapacity * 2);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

### 3.2 `get(int index)`

```java
public synchronized E get(int index) {
    if (index >= elementCount)
        throw new ArrayIndexOutOfBoundsException(index);
    return elementData(index);
}
```

* 查询为 O(1)，效率高。
* 线程安全由 `synchronized` 保证。

---

## 4. 线程安全机制

所有 public 方法都加了 `synchronized`，例如：

```java
public synchronized void removeElementAt(int index)
```

* 保证了多线程下的安全性。
* 但**并发性能差**，因为是方法级别的粗粒度锁。

---

## 5. Vector 与 ArrayList 对比

| 特性     | Vector                 | ArrayList         |
| ------ | ---------------------- | ----------------- |
| 线程安全   | 是（使用 synchronized）     | 否                 |
| 性能     | 较低（线程锁影响）              | 更高                |
| 扩容机制   | 默认翻倍或固定增长              | 默认扩容为 `原容量 * 1.5` |
| 是否推荐使用 | 不推荐（被 Concurrent 工具替代） | 推荐在单线程或通过外部同步使用   |

---


