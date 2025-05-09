# Hashtable

`Hashtable` 是 JDK 1.0 引入的一个 **线程安全**、**哈希表结构**的键值对映射容器，实现了 `Map<K, V>` 接口。

```java
public class Hashtable<K,V> extends Dictionary<K,V> implements Map<K,V>, Cloneable, java.io.Serializable
```

---

## 1. 核心特性

| 特性         | 说明                                                  |
| ---------- | --------------------------------------------------- |
| 线程安全       | 所有方法都使用 `synchronized` 修饰，天生线程安全                    |
| 不允许 `null` | key 和 value 都不能为 `null`，否则抛出 `NullPointerException` |
| 有序性        | 无序，不保证插入顺序或访问顺序                                     |
| 底层结构       | 使用数组 + 链表的哈希表结构（JDK 1.8 前后没有重构为红黑树）                 |

---

## 2. 底层结构

* 使用 `Entry<K, V>[] table` 数组存储元素。
* 每个 bucket 是一个链表，哈希冲突时使用拉链法。
* 所有方法加锁（`synchronized`），**效率较低**，不推荐在高并发下使用。

```java
private static class Entry<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Entry<K,V> next;
}
```

---

## 3. 常用方法

### put(K key, V value)

* 不允许 key 或 value 为 null
* 计算 hash，定位数组索引
* 存在相同 key：覆盖值
* 不存在：头插入链表

### get(Object key)

* 根据 key 计算 hash
* 定位 bucket，遍历链表查找 key
* key 相等则返回对应 value

### remove(Object key)

* 查找 key，断链移除节点

### containsKey / containsValue

* 分别遍历 key 和 value 判断是否存在

### size / isEmpty

* 直接返回字段 `count` 或判断是否为 0

---

