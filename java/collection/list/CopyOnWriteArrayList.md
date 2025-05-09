# CopyOnWriteArrayList

`CopyOnWriteArrayList` 是 Java 提供的**线程安全的 List 实现**，属于 `java.util.concurrent` 包。其核心思想是：

> **“写时复制”（Copy-on-Write）**：每次写操作（如 add/remove/set）都会复制一份底层数组，修改后再替换原数组，确保读操作永远看到一个**稳定一致的快照（snapshot）**。

---

## 1. 类定义

```java
public class CopyOnWriteArrayList<E> implements List<E>, RandomAccess, Cloneable, Serializable
```

* 实现了 `List`，是一个可随机访问的数组结构。
* 线程安全，适合**读多写少**的场景。

---

## 2. 底层结构

```java
private transient volatile Object[] array;
```

* 所有数据保存在一个 `volatile` 的数组中。
* 读操作**无锁**，直接读取 `array`。
* 写操作会**复制一份数组**，在副本上修改，然后**原子替换引用**。

---

## 3. 关键方法

### 3.1 `get(int index)`

```java
public E get(int index) {
    return getArray()[index];
}

final Object[] getArray() {
    return array;
}
```

* **无锁读**，读取的是一个稳定的快照。

---

### 3.2 `add(E e)`

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements); // 替换引用
        return true;
    } finally {
        lock.unlock();
    }
}
```

* 加锁写，复制数组，写入元素后替换引用。

---

### 3.3 `remove(Object o)`

同样会**复制整个数组并重新赋值**：

```java
public boolean remove(Object o) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        // 遍历原数组，构造新数组
        ...
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

---

## 4. 读写对比

| 操作       | 是否加锁 | 是否复制数组 | 线程安全性 |
| -------- | -- | -- | -- |
| get      | 无锁 | 不复制 | 安全 |
| add      | 加锁 | 复制 | 安全 |
| remove   | 加锁 | 复制 | 安全 |
| iterator | 无锁 | 不变 | 快照迭代 |

---

## 5. 迭代器：快照迭代（Snapshot Iterator）

```java
public Iterator<E> iterator() {
    return new COWIterator<>(getArray(), 0);
}
```

* `CopyOnWriteArrayList` 的迭代器是**快照的**，即在构造时复制底层数组，不受后续写操作影响。
* 不支持 `remove()`，是只读的。

优点：

* 避免 `ConcurrentModificationException`
* 并发场景中非常稳定

---

## 6. 适用场景

非常适用于：

* **读远多于写的场景**
* 配置缓存、黑白名单、注册表、观察者模式订阅列表等
* 迭代过程中允许写操作，但要求读一致性

不适用于：

* 高频写（add/remove/set）场景
* 大数据量频繁更新

---

