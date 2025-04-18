# ThreadLocal

`ThreadLocal` 是 Java 提供的一种线程局部变量机制。

每个线程都有自己的局部副本，互不干扰，用于**线程隔离数据**，避免共享带来的线程安全问题。

---

## 1. 常用 API

```java
// 创建一个 ThreadLocal 实例
ThreadLocal<String> local = new ThreadLocal<>();

// 设置值
local.set("Hello");

// 获取值
String value = local.get();

// 删除值（建议手动清理，避免内存泄漏）
local.remove();
```

Java 8 后推荐使用 `withInitial` 创建：
```java
ThreadLocal<String> local = ThreadLocal.withInitial(() -> "default");
```

---


## 2. 底层原理

### Thread 类中：
```java
ThreadLocal.ThreadLocalMap threadLocals;
```

每个线程内部都维护了一个 `ThreadLocalMap`，专门用于存储这个线程对应的所有 ThreadLocal 变量。

### 存储过程

`threadLocal.set(value)`：
- 会将当前线程 `Thread.currentThread()` 的 `threadLocals` Map 中以 `ThreadLocal` 实例为 key，value 为实际值存储。

`threadLocal.get()`：
- 会从当前线程的 `threadLocals` 中读取当前 ThreadLocal 实例对应的值。

> 本质上，`ThreadLocal` 是通过当前线程对象中的 `ThreadLocalMap` 来实现的线程隔离！

---



## 3. 内存泄漏风险点

因为 key 是弱引用，ThreadLocal 对象没引用时会被 GC 回收，但 value 还存在，导致线程长期持有 value（强引用），造成**内存泄漏**。

### 避免办法：

- **手动调用 `remove()` 清理变量**，特别是在线程池中使用时：
```java
try {
    threadLocal.set(value);
    // 业务逻辑
} finally {
    threadLocal.remove(); // 非常关键
}
```

---

## 4. 例子：多线程使用 ThreadLocal

```java
ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 0);

Runnable task = () -> {
    for (int i = 0; i < 3; i++) {
        Integer val = threadLocal.get();
        threadLocal.set(val + 1);
        System.out.println(Thread.currentThread().getName() + " => " + threadLocal.get());
    }
};

new Thread(task).start();
new Thread(task).start();
```

每个线程输出自己的计数器，互不干扰。

---


