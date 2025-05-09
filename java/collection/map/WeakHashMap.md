# WeakHashMap

`WeakHashMap<K, V>` 是一种基于 **弱引用（WeakReference）** 的 `Map` 实现。当某个 key 不再被外部强引用时，其 entry 可能会被垃圾回收器回收。

```java
public class WeakHashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>
```

---


| 特性           | 说明                                                 |
| ------------ | -------------------------------------------------- |
| 弱键（Weak Key） | `WeakHashMap` 的 key 是弱引用，如果没有其他强引用指向 key，它会被 GC 回收 |
| 值（Value）是强引用 | 只有 key 是弱引用，value 是强引用                             |
| 自动移除条目       | GC 回收 key 后，相关 entry 会被 `WeakHashMap` 清除           |
| 非线程安全        | 多线程场景需手动同步或使用 `Collections.synchronizedMap()`      |

---

## 1. 底层实现结构

1. **底层用的是数组 + 链表（哈希表）**
2. **Key 是 `WeakReference<K>` 类型**
3. 内部维护一个 **`ReferenceQueue`**，用于感知哪些 key 被 GC 了

### Entry 结构：

```java
private static class Entry<K,V> extends WeakReference<K> implements Map.Entry<K,V> {
    V value;
    Entry<K,V> next;
    int hash;
}
```

* `Entry` 继承了 `WeakReference<K>`，这意味着它的 key 是弱引用
* `ReferenceQueue<K>`：当 key 被 GC，WeakReference 会被放入这个队列中，方便清理

---

## 2. 关键方法

### 2.1 `put(K, V)`

* 将 key 包装为 `WeakReference<K>`
* 插入哈希表中
* 先调用 `expungeStaleEntries()` 清理掉已被 GC 的条目

### 2.2 `get(K)`

* 找到对应 hash 桶，遍历链表
* 每个节点的 key 是 WeakReference，要调用 `.get()` 获取实际 key 比较

### 2.3 `expungeStaleEntries()`

* 从 `ReferenceQueue` 中取出被 GC 的 WeakReference
* 从哈希表中清除其对应的 Entry

```java
private void expungeStaleEntries() {
    Reference<? extends K> ref;
    while ((ref = queue.poll()) != null) {
        // remove the entry from the hash table
    }
}
```

---

## 3. 与 HashMap 区别

| 特性             | `HashMap` | `WeakHashMap`      |
| -------------- |-----------|--------------------|
| Key 引用类型       | 强引用       | 弱引用（WeakReference） |
| GC 后是否保留 entry | 是         | 否（key 被 GC 后自动移除）  |
| 是否自动清理         | 否         | 是                  |
| 应用场景           | 普通缓存、数据存储 | 缓存、监听器、临时对象表       |

---


## 4. 示例代码

```java
public class WeakHashMapDemo {
    public static void main(String[] args) throws InterruptedException {
        Map<Object, String> map = new WeakHashMap<>();
        Object key = new Object();
        map.put(key, "value");

        System.out.println("Before GC: " + map);
        key = null;
        System.gc();
        Thread.sleep(100);

        System.out.println("After GC: " + map);
    }
}
```

**输出：**

```text
Before GC: {java.lang.Object@xxxx=value}
After GC: {}
```

---

