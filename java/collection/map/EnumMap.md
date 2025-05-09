# EnumMap


`EnumMap` 是 Java 提供的一种专门针对 **枚举类型（`enum`）作为键** 的高性能 `Map` 实现。

```java
public class EnumMap<K extends Enum<K>, V> extends AbstractMap<K, V> implements Serializable, Cloneable
```

它比 `HashMap<Enum, V>` 更高效，内部是数组实现，基于 `enum` 的 ordinal 值（即枚举定义顺序）作为索引。

---

## 1. 核心特性

| 特性              | 说明                 |
| --------------- | ------------------ |
| key 类型          | 必须是 enum 类型        |
| key 比较方式        | 使用 `==`（枚举天然唯一）    |
| key 是否允许 null   | 抛出 `NullPointerException` |
| value 是否允许 null | （但 null 不存储在内部数组）  |
| 底层结构            | 数组                 |
| 有序性             | 按枚举定义顺序            |
| 线程安全性           | 非线程安全              |
| 性能              | 快速、高效，占用内存小        |

---

## 2. 使用示例

```java
enum Day { MON, TUE, WED, THU, FRI, SAT, SUN }

EnumMap<Day, String> schedule = new EnumMap<>(Day.class);
schedule.put(Day.MON, "Meeting");
schedule.put(Day.TUE, "Coding");

System.out.println(schedule.get(Day.MON)); // Meeting
```

---

## 3. 底层原理

### 3.1 数组实现

底层使用两个数组：

* `Object[] values`: 存储 value
* 枚举的 `ordinal()` 方法用作索引

### 3.2 初始化时传入 `Class<K>` 确定枚举的长度和顺序

```java
this.keyUniverse = getKeyUniverse(keyType); // 获取 enum 所有常量（数组）
this.values = new Object[keyUniverse.length];
```

### 3.3 put 操作

```java
int index = key.ordinal();
values[index] = value;
```

### 3.4 get 操作

```java
int index = key.ordinal();
return values[index];
```

---

## 4. 和 HashMap 的对比

| 特性          | `EnumMap` | `HashMap<Enum, V>` |
| ----------- |-----------| ---------------- |
| 内部结构        | 数组        | 哈希表              |
| 性能          | 更快（O(1) 数组访问） | 稍慢（hash + 冲突处理）  |
| 有序性         | 按 enum 顺序 |                  |
| 空间效率        | 更小        | 占用内存更多           |
| null key 支持 | 不支持       |  支持              |

---


## 5. 示例：状态机

```java
enum State { INIT, RUNNING, STOPPED }

EnumMap<State, Runnable> handlers = new EnumMap<>(State.class);
handlers.put(State.INIT, () -> System.out.println("init"));
handlers.put(State.RUNNING, () -> System.out.println("run"));

handlers.get(State.INIT).run();
```

---
