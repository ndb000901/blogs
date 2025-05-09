# TreeSet

`TreeSet` 是 Java 中的**有序 Set 实现**，基于 **红黑树（Red-Black Tree）** 实现。

```java
public class TreeSet<E> extends AbstractSet<E>
        implements NavigableSet<E>, Cloneable, java.io.Serializable
```

### 特点

* 元素 **自动排序（升序）**
* 不允许重复元素
* 查找/删除/添加操作为 O(log n)
* 底层基于 `TreeMap`
* 可通过 `Comparator` 实现自定义排序

---

## 1. 底层结构

`TreeSet` 底层使用 `TreeMap` 实现：

```java
private transient NavigableMap<E,Object> m;

private static final Object PRESENT = new Object();
```

每次 `add(E e)` 实际上是：

```java
m.put(e, PRESENT);
```

---

## 2. 排序机制

### 方式一：元素实现 `Comparable` 接口（自然顺序）

```java
class User implements Comparable<User> {
    public int compareTo(User o) {
        return this.id - o.id;
    }
}
```

### 方式二：构造时传入 `Comparator`

```java
TreeSet<User> set = new TreeSet<>(new Comparator<User>() {
    public int compare(User o1, User o2) {
        return o1.id - o2.id;
    }
});
```

不能同时依赖两者。优先使用构造函数传入的 `Comparator`。

---

## 3. 常用方法

| 方法                            | 说明                      |
| ----------------------------- | ----------------------- |
| `add(E e)`                    | 添加元素，自动排序               |
| `remove(Object o)`            | 删除元素                    |
| `contains(Object o)`          | 判断是否存在                  |
| `first()` / `last()`          | 返回最小 / 最大值              |
| `ceiling(E e)` / `floor(E e)` | 返回大于等于 / 小于等于的最小/最大元素   |
| `higher(E e)` / `lower(E e)`  | 返回严格大于 / 小于给定元素的最小/最大元素 |
| `subSet(E from, E to)`        | 返回指定范围的子集合              |

---

## 4. 性能分析

| 操作   | 时间复杂度    |
| ---- | -------- |
| 插入   | O(log n) |
| 删除   | O(log n) |
| 查找   | O(log n) |
| 有序遍历 | O(n)     |

因为底层是平衡二叉搜索树（红黑树）。

---

## 5. 线程安全性

`TreeSet` **不是线程安全的**。多线程场景下需要手动加锁或使用同步包装：

```java
Set<E> synchronizedSet = Collections.synchronizedSet(new TreeSet<>());
```

---


## 6. 与其他 Set 的对比

| 特性        | HashSet | LinkedHashSet | TreeSet     |
| --------- |---------| -------- | ----------- |
| 底层结构      | HashMap | HashMap + 链表 | TreeMap（红黑树） |
| 是否有序      | 否       | （插入顺序）   | （自动排序）      |
| 是否线程安全    |   否      |     否     |      否       |
| 是否允许 null | （但只能一个） | （但只能一个）  | （会抛异常）      |
| 时间复杂度     | O(1)    | O(1)     | O(log n)    |
| 是否可自定义排序  |    否     |     否     | （传 Comparator） |

---

