# ConcurrentHashMap

`ConcurrentHashMap` 是 Java 中提供的**高性能并发哈希表实现**，用于在多线程环境下替代 `HashMap`。它在保证线程安全的前提下尽可能提高并发性能，是并发编程中非常重要的数据结构。

---

## 1. 基本信息

```java
public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
    implements ConcurrentMap<K,V>, Serializable
```

* 所属包：`java.util.concurrent`
* 出现版本：

  * Java 1.5：基于 Segment 的分段锁实现
  * Java 1.8 起：基于 **CAS + synchronized + 链表/红黑树** 实现，效率更高，分段锁被弃用
* 线程安全：支持高并发的读写
* 底层结构：数组 + 链表 / 红黑树（与 HashMap 类似）

---

## 2. 核心特性

| 特性              | 说明                        |
| --------------- | ------------------------- |
| 线程安全            | 多线程并发访问安全                 |
| key 是否允许 null   | 不允许（会抛出 NullPointerException） |
| value 是否允许 null | 不允许                       |
| 排序              | 无序                        |
| 结构              | 数组 + 链表/红黑树（Java 8+）      |
| 并发性             | 支持高并发读，写是局部锁定或 CAS 操作     |
| 替代方案            | 替代 Hashtable / SynchronizedMap |

---

## 3. 主要构造方法

```java
new ConcurrentHashMap<>(); // 默认初始容量16，负载因子0.75
new ConcurrentHashMap<>(int initialCapacity);
new ConcurrentHashMap<>(int initialCapacity, float loadFactor);
new ConcurrentHashMap<>(int initialCapacity, float loadFactor, int concurrencyLevel); // Java 7 及以前
new ConcurrentHashMap<>(Map<? extends K, ? extends V> m);
```

---

## 4. 主要方法

| 方法名                                          | 说明                      |
| -------------------------------------------- | ----------------------- |
| `put(K key, V value)`                        | 添加或更新 key 的值（线程安全）      |
| `get(Object key)`                            | 获取 key 的值（线程安全，读无锁）     |
| `remove(Object key)`                         | 删除 key                  |
| `containsKey(Object key)`                    | 判断是否包含 key              |
| `putIfAbsent(K key, V value)`                | 如果 key 不存在，才放入          |
| `replace(K key, V old, V new)`               | CAS 替换值（只有旧值等于 old 才替换） |
| `computeIfAbsent`、`computeIfPresent`、`merge` | 支持函数式编程（Java 8+）        |

---

## 5. Java 8 实现原理

### 底层结构（Java 8）

```java
Node<K,V>[] table;         // 主数组
Node<K,V>[] nextTable;     // 扩容时的临时数组
```

每个 Node 结构如下：

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    volatile V val;
    volatile Node<K,V> next;
}
```

### 插入逻辑（put）

1. 计算 hash
2. 如果数组为空，则初始化
3. 寻找 bin（桶），如果是空桶则 `CAS` 插入
4. 如果非空，使用 `synchronized` 锁住桶头节点
5. 遇到链表长度超过阈值（默认8）则转为红黑树

> 整体无段锁结构，只有桶级锁 + CAS，避免了全表锁竞争。

---

## 6. 并发特性

### 6.1 高并发读

* 读取使用 `volatile` + 内存可见性，**无锁读**
* 比 Hashtable 和 SynchronizedMap 性能更高

### 6.2 局部加锁写

* 写操作仅锁住桶中的链表头节点（Java 8 使用 `synchronized`）
* 大大降低锁冲突，提高并发度

### 6.3 扩容过程

* 与 `HashMap` 一样，扩容为 2 倍
* 使用 `transfer` 方法将旧数组元素迁移到新数组
* 多线程并发迁移（通过 `forwardingNode` 标记）

---



