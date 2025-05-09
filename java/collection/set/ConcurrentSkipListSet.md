# ConcurrentSkipListSet

`ConcurrentSkipListSet` 是 Java 并发包下的一个 **线程安全、有序、不重复集合**：

```java
public class ConcurrentSkipListSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, Serializable
```

* 属于 `java.util.concurrent` 包
* 底层使用 **跳表（Skip List）** 实现
* **自动排序（基于元素自然顺序或 Comparator）**
* **线程安全（通过无锁 CAS 实现）**

---

## 1. 底层结构：跳表（Skip List）

### 跳表

跳表是一种基于**多级链表结构**的平衡查找结构，提供类似于二叉搜索树的对数复杂度，但实现更简单、适合并发场景。

跳表特点：

* 多层索引，最高层跨度最大
* 平均插入/删除/查找复杂度为 **O(log n)**
* 底层是有序链表

### 并发跳表实现：

`ConcurrentSkipListSet` 内部基于 `ConcurrentSkipListMap` 实现，使用了：

* **无锁化算法（Lock-Free）**
* **CAS + volatile 保证线程安全**
* **节点通过多个指针引用上层、下层节点**

```java
private final ConcurrentNavigableMap<E,Object> map;
private static final Object PRESENT = new Object();
```

每个元素都是 `map.put(e, PRESENT);`

---

## 2. 构造函数

```java
// 默认使用自然排序
new ConcurrentSkipListSet<>();

// 使用自定义比较器
new ConcurrentSkipListSet<>(Comparator.comparing(String::length));

// 从已有集合构造
new ConcurrentSkipListSet<>(existingSet);
```

---

## 3. 常用方法

| 方法                   | 说明                  |
| -------------------- | ------------------- |
| `add(E e)`           | 添加元素                |
| `remove(Object o)`   | 删除元素                |
| `contains(Object o)` | 是否存在                |
| `first()` / `last()` | 最小 / 最大元素           |
| `ceiling(E e)`       | ≥ e 的最小元素           |
| `floor(E e)`         | ≤ e 的最大元素           |
| `higher(E e)`        | > e 的最小元素           |
| `lower(E e)`         | < e 的最大元素           |
| `subSet(from, to)`   | 范围子集（from ≤ x < to） |

---

## 4. 线程安全性分析

* **完全线程安全**，适用于高并发读写
* 无需外部加锁
* 内部通过 `Unsafe` 的 `compareAndSwapXXX` 保证操作原子性

**弱一致性迭代器**，迭代过程中不会抛异常，但也不保证实时一致。

---

## 5. 性能

| 操作 | 时间复杂度    |
| -- | -------- |
| 添加 | O(log n) |
| 删除 | O(log n) |
| 查找 | O(log n) |
| 遍历 | O(n)     |

> 比 HashSet 慢，但支持排序 & 并发，是并发场景下的 TreeSet 替代品。

---


## 6. 与其他 Set 的对比

| 特性           | HashSet | TreeSet | CopyOnWriteArraySet | ConcurrentSkipListSet |
| ------------ | ------- | ------- | ------------------- | --------------------- |
| 是否有序         | 否       | 是（红黑树）  | 是（插入顺序）             | 是（跳表）                 |
| 是否线程安全       | 否       | 否       | 是                   | 是                     |
| 并发写入支持       | 否       | 否       | 一次写入全拷贝             | 高效并发写入                |
| 是否支持排序查询     | 否       | 是       | 否                   | 是                     |
| 是否允许 null 元素 | 是       | 否       | 是                   | 否（抛 NPE）              |
| 遍历一致性        | 强一致性    | 强一致性    | 快照一致                | 弱一致性                  |
| 适用场景         | 单线程、无序  | 单线程、有序  | 读多写少                | 并发、需排序                |

---

