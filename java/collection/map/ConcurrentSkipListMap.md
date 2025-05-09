# ConcurrentSkipListMap

`ConcurrentSkipListMap` 是 Java 提供的一种**基于跳表（Skip List）实现的并发有序 Map**，它支持**高并发读写**，并且内部 key 始终是**按自然顺序或指定比较器顺序**排序的。

---

```java
public class ConcurrentSkipListMap<K, V>
    extends AbstractMap<K, V>
    implements ConcurrentNavigableMap<K, V>, Cloneable, Serializable
```

* 所属包：`java.util.concurrent`
* 底层结构：**跳表（Skip List）**
* 排序方式：按 key 的**自然顺序**或**自定义 Comparator**
* 线程安全：支持高并发，非阻塞读，局部同步写
* 主要接口：`ConcurrentNavigableMap`（继承自 `NavigableMap`）

---

## 1. 核心特性

| 特性                 | 支持                          |
| ------------------ |-----------------------------|
| 线程安全               | 支持                          |
| key/value 允许为 null | 全部不允许                       |
| 有序性                | 按 key 排序（如 TreeMap）         |
| 并发读                | 高效无锁                        |
| 并发写                | 局部加锁                        |
| 可用于并发导航视图          | 支持 subMap/headMap/tailMap 等 |

---

## 2. 常用构造方法

```java
ConcurrentSkipListMap<K, V> map = new ConcurrentSkipListMap<>();
ConcurrentSkipListMap<K, V> map2 = new ConcurrentSkipListMap<>(Comparator<K> cmp);
ConcurrentSkipListMap<K, V> map3 = new ConcurrentSkipListMap<>(Map<? extends K, ? extends V> m);
```

---

## 3. 主要方法

`ConcurrentSkipListMap` 实现了 `NavigableMap` 接口的大部分方法，包括：

### 基本操作

| 方法                    | 描述          |
| --------------------- | ----------- |
| `put(K, V)`           | 添加或更新键值对    |
| `get(Object key)`     | 获取 key 对应值  |
| `remove(Object key)`  | 删除 key      |
| `containsKey(Object)` | 判断 key 是否存在 |

### 排序导航

| 方法                  | 描述               |
| ------------------- | ---------------- |
| `firstKey()`        | 返回最小的 key        |
| `lastKey()`         | 返回最大的 key        |
| `higherKey(K key)`  | 返回比 key 大的最小 key |
| `lowerKey(K key)`   | 返回比 key 小的最大 key |
| `ceilingKey(K key)` | 返回 ≥ key 的最小 key |
| `floorKey(K key)`   | 返回 ≤ key 的最大 key |

### 子视图支持（重要）

| 方法                       | 描述                          |
| ------------------------ | --------------------------- |
| `subMap(fromKey, toKey)` | 返回子区间 \[fromKey, toKey) 的视图 |
| `headMap(toKey)`         | 小于 toKey 的视图                |
| `tailMap(fromKey)`       | 大于等于 fromKey 的视图            |
| `descendingMap()`        | 反序视图                        |

---

## 4. 底层原理：跳表（Skip List）

跳表是一种 **分层链表** 结构，具备如下特性：

* 查询、插入、删除：平均 O(log n)
* 结构：每个节点可以存在于多层链表中（类似多级高速公路）
* 插入新元素时，随机决定层数（概率提升）

优点：

* 易于实现
* 支持并发性好（相比红黑树不需要全局重排）

在 Java 实现中：

* 每个节点使用 `CAS` 操作更新指针
* 读操作**无需加锁**
* 写操作使用**局部同步**

---

## 5. 线程安全实现机制

* **读操作**是**无锁**的，基于 volatile 变量和 CAS 实现可见性和一致性
* **写操作**（如 put/remove）是局部同步，粒度较小，性能远高于 `TreeMap + synchronized` 的组合
* **插入/删除节点时**会保证跳表结构的一致性，使用的是 optimistic concurrency control（乐观锁）

---



## 6. 示例代码

```java
ConcurrentSkipListMap<Integer, String> map = new ConcurrentSkipListMap<>();

map.put(10, "ten");
map.put(5, "five");
map.put(20, "twenty");

System.out.println(map); // {5=five, 10=ten, 20=twenty}

System.out.println(map.firstKey());   // 5
System.out.println(map.lastKey());    // 20
System.out.println(map.higherKey(10));// 20

// 子视图
NavigableMap<Integer, String> head = map.headMap(10);
System.out.println(head); // {5=five}
```

---

