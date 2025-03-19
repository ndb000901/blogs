# java 集合

在 Java 集合框架（JCF）中，常用的集合类可以分为以下几类：  

---

## 1. `List`（有序、可重复）  
`List` 允许存储重复元素，并保持元素插入的顺序。

| 类名 | 底层结构 | 线程安全 | 适用场景 |
|------|---------|---------|---------|
| `ArrayList` | 动态数组 | 否 | 读多写少，查询快 |
| `LinkedList` | 双向链表 | 否 | 频繁插入、删除 |
| `Vector` | 动态数组 | 是 | 线程安全，但性能低 |
| `Stack` | 继承 `Vector` | 是 | 线程安全的栈 |

### 常用 `List` 操作
```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");
System.out.println(list.get(1)); // B
list.remove("A");
System.out.println(list.size()); // 2
```

---

## 2. `Set`（无序、不可重复）  
`Set` 主要用于去重，不允许存储重复元素。

| 类名 | 底层结构 | 是否有序 | 线程安全 | 适用场景 |
|------|---------|---------|---------|---------|
| `HashSet` | `HashMap`（Key） | 否 | 否 | 无序去重，查询快 |
| `LinkedHashSet` | `HashMap + 双向链表` | 是 | 否 | 需要维护插入顺序 |
| `TreeSet` | `TreeMap`（红黑树） | 是（排序） | 否 | 需要排序 |

### 常用 `Set` 操作
```java
Set<String> set = new HashSet<>();
set.add("A");
set.add("B");
set.add("A"); // 无法添加重复元素
System.out.println(set.size()); // 2
```

---

## 3. `Queue`（队列，FIFO）  
`Queue` 主要用于先进先出（FIFO）的数据结构。

| 类名 | 底层结构 | 线程安全 | 适用场景 |
|------|---------|---------|---------|
| `LinkedList` | 双向链表 | 否 | 可用作队列或栈 |
| `PriorityQueue` | 堆 | 否 | 按优先级排序 |
| `ArrayDeque` | 循环数组 | 否 | 双端队列 |

### 常用 `Queue` 操作
```java
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);
queue.offer(2);
System.out.println(queue.poll()); // 1
```

---

## 4. `Map`（键值对存储，Key 不可重复）  
`Map` 主要用于键值对存储，Key 不能重复，Value 可以重复。

| 类名 | 底层结构 | 是否有序 | 线程安全 | 适用场景 |
|------|---------|---------|---------|---------|
| `HashMap` | 哈希表 | 否 | 否 | 快速存取 |
| `LinkedHashMap` | 哈希表 + 双向链表 | 是 | 否 | 需要顺序 |
| `TreeMap` | 红黑树 | 是（按 key 排序） | 否 | 需要排序 |
| `Hashtable` | 哈希表 | 否 | 是 | 线程安全，性能低 |
| `ConcurrentHashMap` | 分段哈希表 | 否 | 是 | 高并发场景 |

### 常用 `Map` 操作
```java
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put("B", 2);
System.out.println(map.get("A")); // 1
```

---

## 5. `Collections` 工具类  
`Collections` 提供了一系列静态方法来操作集合，如排序、查找、同步等。

### 常用 `Collections` 方法：
```java
List<Integer> list = Arrays.asList(3, 1, 2);
Collections.sort(list);
System.out.println(list); // [1, 2, 3]
```

---

## 6. Java 8 `Stream` API 操作集合  
Java 8 引入 `Stream` API，可以对集合进行高效处理。

### 过滤：
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
List<String> filtered = names.stream()
    .filter(name -> name.startsWith("A"))
    .collect(Collectors.toList());
System.out.println(filtered); // [Alice]
```

### 映射：
```java
List<Integer> lengths = names.stream()
    .map(String::length)
    .collect(Collectors.toList());
System.out.println(lengths); // [5, 3, 7]
```

---

## 7. 线程安全集合  
**非线程安全集合可以通过 `Collections.synchronizedXXX()` 方法转换为线程安全版本。**

```java
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
Map<String, String> syncMap = Collections.synchronizedMap(new HashMap<>());
```

---

## 8. 总结  
Java 常用集合类及其适用场景：

- **List**（有序、可重复）：`ArrayList`、`LinkedList`、`Vector`
- **Set**（无序、去重）：`HashSet`、`LinkedHashSet`、`TreeSet`
- **Queue**（FIFO/优先级）：`LinkedList`、`PriorityQueue`
- **Map**（键值对存储）：`HashMap`、`TreeMap`、`Hashtable`
- **工具类**：`Collections`、`Arrays`
- **Stream API**：高效集合操作


ArrayList
LinkedList
HashSet
HashMap



