# TreeMap

`TreeMap` 是 Java 中基于 **红黑树（Red-Black Tree）** 实现的有序 `Map`，其键按照自然顺序（`Comparable`）或提供的 `Comparator` 进行排序，适用于需要**排序、范围查找、按顺序遍历**的场景。

---

```java
public class TreeMap<K,V> extends AbstractMap<K,V>
    implements NavigableMap<K,V>, Cloneable, Serializable
```

* 属于 `java.util` 包
* 实现了 `NavigableMap`、`SortedMap` 接口
* 底层是 **红黑树**，即一种自平衡的二叉搜索树（BST）

---

## 1. 核心特性

| 特性         | 说明                              |
| ---------- | ------------------------------- |
| 排序方式       | 支持自然排序（`Comparable`）或定制排序（`Comparator`） |
| 键是否允许 null | 不允许 null（会抛异常）                  |
| 值是否允许 null | 允许                              |
| 有序性        | 按照键排序                           |
| 线程安全性      | 非线程安全                           |
| 性能         | O(logN) 插入、删除、查找（因红黑树）          |

---

## 2. 构造函数

```java
TreeMap()                          // 使用自然顺序（key 实现了 Comparable）
TreeMap(Comparator<? super K>)    // 使用自定义 Comparator
TreeMap(Map<? extends K, ? extends V>) // 复制其他 Map 内容并排序
TreeMap(SortedMap<K, ? extends V>)     // 复制已有有序 Map 内容
```

---

## 3. 使用示例

### 3.1 默认自然排序

```java
TreeMap<Integer, String> map = new TreeMap<>();
map.put(3, "C");
map.put(1, "A");
map.put(2, "B");

System.out.println(map); // {1=A, 2=B, 3=C}
```

### 3.2 使用自定义 Comparator

```java
TreeMap<String, Integer> map = new TreeMap<>(Comparator.reverseOrder());
map.put("apple", 1);
map.put("banana", 2);
System.out.println(map); // {banana=2, apple=1}
```

---

## 4. API

### 4.1 排序相关

| 方法                       | 说明             |
| ------------------------ | -------------- |
| `firstKey()`             | 返回最小 key       |
| `lastKey()`              | 返回最大 key       |
| `higherKey(k)`           | 严格大于 k 的最小 key |
| `lowerKey(k)`            | 严格小于 k 的最大 key |
| `ceilingKey(k)`          | ≥k 的最小 key     |
| `floorKey(k)`            | ≤k 的最大 key     |
| `subMap(fromKey, toKey)` | 范围视图           |
| `descendingMap()`        | 返回逆序视图         |

### 4.2 视图

* `keySet()`：返回有序的 key 集合
* `values()`：值集合（无序）
* `entrySet()`：有序键值对集合

---

## 5. 底层原理

### 数据结构：红黑树

红黑树特性：

* 每个节点红或黑
* 根节点黑色
* 红色节点不能连续出现
* 从任一节点到其所有后代叶节点的路径包含相同数量的黑节点
* 保证树高度 ≤ 2log(n+1)，插入、删除时间复杂度 O(logN)

### 节点结构（内部类 Entry）

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
    K key;
    V value;
    Entry<K,V> left, right, parent;
    boolean color = BLACK; // true = RED, false = BLACK
}
```

### 插入过程

* 先按照比较器查找插入位置
* 插入新节点并默认为红色
* 通过旋转和变色维护红黑树平衡

---

## 6. TreeMap 与 HashMap 对比

| 特性          | TreeMap                   | HashMap     |
| ----------- | ------------------------- | ----------- |
| 底层结构        | 红黑树                       | 数组 + 链表/红黑树 |
| key 是否有序    | 是，自动排序                    | 否，无序        |
| null key 支持 | 不支持                       | 允许一个 null key |
| 性能          | O(logN) 查找                | O(1) 平均查找   |
| 可用于范围查询     | subMap, headMap, tailMap 支持 | 不支持范围查询     |

---

