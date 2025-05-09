# HashSet


`HashSet` 是 Java 集合框架中最常用的 `Set` 实现类之一，底层基于 `HashMap` 实现，用于存储**不重复的元素**。

```java
public class HashSet<E> extends AbstractSet<E> implements Set<E>, Cloneable, java.io.Serializable
```

---

## 1. 底层结构

* 实际是通过 `HashMap<E, Object>` 来存储元素，Set 中的每个元素是这个 `Map` 的 **key**，而所有的 value 都是一个常量对象 `PRESENT`。
* 插入元素时，调用 `HashMap.put(key, PRESENT)` 实现。

### 源码关键字段：

```java
private transient HashMap<E, Object> map;
private static final Object PRESENT = new Object();
```

---

## 2. 构造方法

```java
public HashSet() {
    map = new HashMap<>();
}

public HashSet(int initialCapacity) {
    map = new HashMap<>(initialCapacity);
}

public HashSet(int initialCapacity, float loadFactor) {
    map = new HashMap<>(initialCapacity, loadFactor);
}

public HashSet(Collection<? extends E> c) {
    map = new HashMap<>(Math.max((int) (c.size()/.75f) + 1, 16));
    addAll(c);
}
```

---

## 3. 核心方法

### 3.1 添加元素 `add(E e)`

```java
public boolean add(E e) {
    return map.put(e, PRESENT) == null;
}
```

* 实际是调用了 `HashMap.put()`。
* 如果 key 已经存在，则 `put` 返回旧值（不为 null），因此 `add()` 返回 false，表示添加失败（元素重复）。

### 3.2 删除元素 `remove(Object o)`

```java
public boolean remove(Object o) {
    return map.remove(o) == PRESENT;
}
```

### 3.3 是否包含 `contains(Object o)`

```java
public boolean contains(Object o) {
    return map.containsKey(o);
}
```

### 3.4 清空集合

```java
public void clear() {
    map.clear();
}
```

### 3.5 获取迭代器

```java
public Iterator<E> iterator() {
    return map.keySet().iterator();
}
```

---

## 4. HashSet 的特性

| 特性      | 说明                              |
| ------- | ------------------------------- |
| 元素唯一    | 根据 `hashCode()` 和 `equals()` 判断 |
| 插入无序    | 不保证元素顺序                         |
| 允许 null | 只允许一个 null 元素                   |
| 线程不安全   | 多线程需外部加锁或用并发版本                  |
| 性能      | 查询、插入、删除的平均时间复杂度为 O(1)（散列均匀时）   |

---

## 5. 重要注意事项

### 5.1 自定义对象必须重写 `equals()` 和 `hashCode()`

示例：

```java
class User {
    String name;
    int age;

    @Override
    public boolean equals(Object o) {
        // 按 name 和 age 判断
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

如果不重写，则默认 `equals` 是地址比较，无法实现去重。

---

### 5.2 null 的支持情况

```java
HashSet<String> set = new HashSet<>();
set.add(null);
set.add(null); // 第二次添加失败
System.out.println(set.size()); // 1
```

---

## 6. HashSet 的扩容机制（通过 HashMap）

* 初始容量为 16，加载因子为 0.75。
* 当元素数量超过 `容量 × 0.75` 时，会触发扩容（容量翻倍）。
* JDK 8 中 `HashMap` 会将链表转换为红黑树（链表长度 ≥ 8 且容量 ≥ 64），提升查询效率。

---

## 7. HashSet 与其它 Set 对比

| 特性        | HashSet | LinkedHashSet | TreeSet                     |
| --------- | ------ | ------------ | --------------------------- |
| 底层结构      | HashMap | HashMap + 链表 | TreeMap（红黑树）                |
| 是否有序      | 否      | 插入顺序         | 自动排序                        |
| 是否线程安全    | 否      | 否            | 否                           |
| 是否允许 null | （一个）   | （一个）         | （不允许）                       |
| 查询效率      | 高（O(1)） | 高            | 中（O(logN)）                  |
| 元素要求      | 无要求    | 无要求          | 实现 Comparable 或提供 Comparator |

---

