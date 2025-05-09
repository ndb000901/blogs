# LinkedHashSet

`LinkedHashSet` 是 Java 集合框架中的一个 Set 实现类，继承自 `HashSet`，基于 `LinkedHashMap` 实现，具有 **元素唯一性 + 插入顺序** 的特点。

```java
public class LinkedHashSet<E> extends HashSet<E> implements Set<E>, Cloneable, Serializable
```

---

## 1. 底层结构


`LinkedHashSet` 通过内部的 `LinkedHashMap<E, Object>` 来保存元素，和 `HashSet` 的实现类似，但它维护了一个 **双向链表**，用于记录元素的插入顺序。

```java
private transient LinkedHashMap<E, Object> map;
private static final Object PRESENT = new Object();
```

* 每一个元素作为 `map` 的 key；
* `PRESENT` 是一个固定值；
* 插入顺序由 `LinkedHashMap` 维护。

---

## 2. 构造函数

```java
public LinkedHashSet() {
    map = new LinkedHashMap<>();
}

public LinkedHashSet(int initialCapacity) {
    map = new LinkedHashMap<>(initialCapacity);
}

public LinkedHashSet(int initialCapacity, float loadFactor) {
    map = new LinkedHashMap<>(initialCapacity, loadFactor);
}

public LinkedHashSet(int initialCapacity, float loadFactor, boolean accessOrder) {
    map = new LinkedHashMap<>(initialCapacity, loadFactor, accessOrder);
}
```

> `accessOrder = true`：可以实现 **访问顺序排序**，默认是 `false`（插入顺序）。

---

## 3. 核心方法

### 3.1 添加元素

```java
public boolean add(E e) {
    return map.put(e, PRESENT) == null;
}
```

* 内部实际调用的是 `LinkedHashMap.put()`。

### 3.2 删除元素

```java
public boolean remove(Object o) {
    return map.remove(o) == PRESENT;
}
```

### 3.3 判断是否存在

```java
public boolean contains(Object o) {
    return map.containsKey(o);
}
```

### 3.4 获取迭代器

```java
public Iterator<E> iterator() {
    return map.keySet().iterator();
}
```

> 元素的遍历顺序就是它们被添加的顺序。

---

## 4. 与 `HashSet` 的区别

| 特性     | HashSet | LinkedHashSet |
| ------ | ----- | ----------- |
| 底层结构   | HashMap | LinkedHashMap |
| 是否保证顺序 | 无序    | 保证插入顺序或访问顺序 |
| 查询效率   | O(1)  | O(1)        |
| 内存占用   | 相对较少  | 略高（需维护链表）   |
| 适合场景   | 仅需去重  | 去重 + 记录插入顺序 |

---

