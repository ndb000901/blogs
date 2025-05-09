# IdentityHashMap


`IdentityHashMap` 是 Java 提供的一种特殊 `Map` 实现，它使用 **对象的内存地址**（也就是 `==`）来比较 key，而不是 `equals()` 方法。

```java
public class IdentityHashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Serializable, Cloneable
```

---

## 1. 核心特性

| 特性          | 描述                        |
| ----------- | ------------------------- |
| key 比较方式    | 使用 `==` 比较（即是否是同一个对象）     |
| hashCode 算法 | 使用 `System.identityHashCode()` |
| 是否允许 null   | （允许 `null` 作为 key/value）  |
| 是否线程安全      | （非线程安全）                   |
| 是否有序        | 无顺序保证）                    |
| 是否允许重复 key  | （相同内容但不同引用的对象可共存）         |

---

## 2. 应用场景

* 当你需要区分两个内容相同但引用不同的对象作为 key。
* JVM/类加载器/缓存框架中，用于存储对象元数据或标记。

---

## 3. 与 `HashMap` 的区别

| 特性          | `HashMap`     | `IdentityHashMap`           |
| ----------- | ------------- | --------------------------- |
| key 比较方式    | `.equals()`   | `==`                        |
| hashCode 来源 | `.hashCode()` | `System.identityHashCode()` |
| 用于区分对象值还是引用 | 区分值相等即可       | 区分对象引用是否相同                  |
| 应用场景        | 一般业务逻辑        | 框架/底层优化/性能特殊场景              |

---

## 4. 底层结构

* `IdentityHashMap` 使用 **Object\[] 双倍数组**（key 和 value 交替存放），而不是链表或树。

```java
transient Object[] table;
```

数组格式：

```text
[ key0, value0, key1, value1, key2, value2, ... ]
```

* 初始容量必须是 2 的幂（类似 `HashMap`）。
* 使用线性探测解决哈希冲突。

---

