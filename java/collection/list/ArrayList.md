# ArrayList


`ArrayList` 是基于 **动态数组** 实现的可变长度数组，属于 **非线程安全** 的集合类，提供了快速的随机访问能力。

```java
public class ArrayList<E> extends AbstractList<E> 
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

* `RandomAccess`：支持快速随机访问（即 `get()` 是 O(1)）。
* `Cloneable`：支持浅拷贝。
* `Serializable`：支持序列化。

---

## 1. 底层结构

核心字段如下：

```java
transient Object[] elementData; // 存储元素的数组
private int size;               // 实际元素个数
```

### 初始容量

```java
// 默认构造器不会立即分配数组（lazy load）
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

首次 `add()` 时触发分配，默认容量为 10。

---

## 2. 扩容机制（ensureCapacity、grow）

核心方法：

```java
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1); // 扩容1.5倍
    if (newCapacity < minCapacity)
        newCapacity = minCapacity;
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

特点：

* **扩容成本高**：拷贝旧数组到新数组。
* **不是每次 add 都扩容**，而是在数组满时才触发。

---

## 3. 常用方法

### 3.1 `add(E e)`

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1); // 确保容量
    elementData[size++] = e;
    return true;
}
```

### 3.2 `get(int index)`

```java
public E get(int index) {
    rangeCheck(index);
    return elementData(index);
}
```

是 O(1) 操作，非常快。

### 3.3 `remove(int index)`

```java
public E remove(int index) {
    rangeCheck(index);
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work
    return oldValue;
}
```

* 删除后，所有后面的元素需要左移，代价为 `O(n)`。

### 3.4 `clear()`

```java
public void clear() {
    for (int i = 0; i < size; i++)
        elementData[i] = null;
    size = 0;
}
```

---

## 4. 性能分析

| 操作          | 时间复杂度 | 备注        |
| ----------- | ---- | --------- |
| `get(int)`  | O(1) | 通过下标直接访问  |
| `add(E)`    | O(1) | 摊销，偶尔触发扩容 |
| `remove(i)` | O(n) | 后面元素需整体左移 |
| 插入中间元素      | O(n) | 后移元素      |

---


