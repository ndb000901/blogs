# LinkedHashMap


```java
public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>
```

* `LinkedHashMap` 是 `HashMap` 的子类。
* 与 `HashMap` 的区别：它维护了一个**双向链表**，记录元素的**插入顺序**或**访问顺序**。
* 是有序的 `Map` 实现。

---

## 1. 底层结构

### 1.1 核心数据结构

`LinkedHashMap` 继承了 `HashMap` 的数组 + 链表/红黑树结构，并添加了维护顺序的双向链表：

```text
HashMap（数组 + 链表 + 红黑树） + 双向链表（记录顺序）
```

### 1.2 双向链表节点结构

它重写了 `HashMap` 的 `Node<K,V>` 类，使用了：

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after; // 双向链表指针
}
```

---

## 2. 构造方法

```java
// 默认插入顺序
new LinkedHashMap<>();

// 访问顺序（LRU 实现依赖）
new LinkedHashMap<>(initialCapacity, loadFactor, true);
```

| 参数                    | 含义              |
| --------------------- | --------------- |
| `accessOrder = false` | 默认，按照**插入顺序**维护 |
| `accessOrder = true`  | 按照**访问顺序**维护    |

---

## 3. 核心方法

### 3.1 `put(K, V)`

插入元素时：

* 会插入到 HashMap 的 table 中
* 同时更新双向链表结构：插入到链表尾部

### 3.2 `get(K)`

* 正常查找值（跟 HashMap 一样）
* 如果是 **accessOrder=true**，会**将当前节点移动到链表尾部**

### 3.3 `removeEldestEntry(Map.Entry<K,V> eldest)`

此方法用于实现 **LRU 缓存淘汰策略**，可以通过重写该方法自动删除最旧的 entry：

```java
@Override
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
    return size() > MAX_CAPACITY;
}
```

配合 accessOrder = true 可实现 **最近最少使用（LRU）缓存算法**。

---

## 4. 与 HashMap 的区别

| 特性     | HashMap | LinkedHashMap            |
| ------ | ------ | ------------------------ |
| 顺序     | 无序     | 插入顺序（或访问顺序）              |
| 迭代顺序   | 不确定    | 确定（插入/访问顺序）              |
| LRU 支持 |        | （重写 `removeEldestEntry`） |
| 性能     | 稍快     | 稍慢（维护链表开销）               |

---

## 5. 线程安全

* **不是线程安全的**
* 如需多线程使用，可通过外部加锁或 `Collections.synchronizedMap(...)` 包装

---


## 6. 示例：LRU 缓存实现

```java
class LRUCache<K,V> extends LinkedHashMap<K,V> {
    private final int MAX_CAPACITY;

    public LRUCache(int capacity) {
        super(capacity, 0.75f, true); // accessOrder = true
        this.MAX_CAPACITY = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
        return size() > MAX_CAPACITY;
    }
}
```

---


