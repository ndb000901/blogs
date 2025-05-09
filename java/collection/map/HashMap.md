# HashMap

```java
public class HashMap<K, V> extends AbstractMap<K, V>
    implements Map<K, V>, Cloneable, Serializable
```

* **底层结构：** 数组 + 链表（JDK 1.7） / 数组 + 链表 + 红黑树（JDK 1.8+）
* **线程安全：** 非线程安全（多线程环境需使用 `ConcurrentHashMap`）
* **键/值允许为 null：** 允许一个 `null` 键，多个 `null` 值
* **负载因子（Load Factor）：** 默认 0.75，控制空间和时间的平衡

---

## 1. 底层结构（JDK 1.8）

```text
table 数组（Node<K,V>[]）
     ↓
   Node（链表）
     ↓
 红黑树（当链表长度 ≥ 8 且数组长度 ≥ 64 时转为红黑树）
```

### 重要属性：

| 字段                                        | 含义                           |
| ----------------------------------------- | ---------------------------- |
| `transient Node<K,V>[] table`             | 存储数据的主结构                     |
| `int size`                                | 当前元素个数                       |
| `int threshold`                           | 扩容阈值（capacity \* loadFactor） |
| `float loadFactor`                        | 负载因子                         |
| `final float DEFAULT_LOAD_FACTOR = 0.75f` | 默认负载因子                       |
| `DEFAULT_INITIAL_CAPACITY = 16`           | 默认初始容量                       |

---

## 2. 核心接口

### 2.1 `put(K key, V value)`

流程如下：

1. `key.hashCode()` 获取哈希值，经过扰动函数处理得到 hash
2. `index = hash & (table.length - 1)` 计算插入位置
3. 若该位置为空，直接插入
4. 若有冲突，使用链表/红黑树查找是否已有该 key

   * 有：覆盖旧值
   * 无：尾插新节点
5. 若链表长度超阈值（默认 8），且数组长度 ≥ 64，转红黑树
6. 若 size 超过 threshold，进行扩容

### 2.2 `get(Object key)`

1. 计算 key 的 hash 值
2. 定位数组索引
3. 在该桶中查找（遍历链表或红黑树）

---

## 3. 扩容机制

* 每次扩容：**容量 × 2**
* 新旧数组映射时，**原索引 i 的节点，要么仍在 i，要么在 i + oldCap**

```java
resize() // 在 put 时触发
```

扩容的代价很高（重新计算 hash & 迁移节点），应合理设置初始容量。

---

## 4. 红黑树化条件（JDK 1.8）

| 触发条件             | 含义          |
| ---------------- | ----------- |
| 链表长度 ≥ 8         | 树化          |
| 数组长度 ≥ 64        | 树化生效（否则先扩容） |
| 树退化为链表（删除元素） < 6 | 退化回链表       |

---

## 5. Hash 冲突处理

* 通过 `链表` 和 `红黑树` 解决 hash 冲突
* key 冲突时通过 `equals()` 判断是否为同一个 key

---

## 6. 线程安全问题

* `HashMap` 是非线程安全的。
* 在多线程下可能出现：

  * 数据丢失
  * 死循环（JDK 1.7 扩容时链表形成环形）

解决方式：

* 使用 `Collections.synchronizedMap(new HashMap<>())`
* 推荐使用 `ConcurrentHashMap`

---