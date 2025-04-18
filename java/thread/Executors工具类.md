# Executors 工具类



`Executors` 是 JDK 并发包中提供的一个**线程池工厂工具类**，用来简化线程池的创建。

它的目标是：
> **快速便捷地创建不同类型的线程池**。

它主要提供了一些静态工厂方法，用来创建 `Executor`、`ExecutorService`、`ScheduledExecutorService` 等接口的实现类。

---

## 1. 常用方法及线程池类型

### 1.1  `newFixedThreadPool(int nThreads)`
创建一个**固定大小**的线程池（核心线程数 = 最大线程数）。

```java
ExecutorService pool = Executors.newFixedThreadPool(5);
```

特点：
- 核心线程数 = 最大线程数，不会回收
- 使用 `LinkedBlockingQueue`（无界队列）

适合：
- 比较稳定、长期的任务处理

---

### 1.2 `newCachedThreadPool()`
创建一个**可缓存的线程池**，线程数无限制，空闲 60 秒会被回收。

```java
ExecutorService pool = Executors.newCachedThreadPool();
```

特点：
- 核心线程数 = 0，最大线程数 = Integer.MAX_VALUE
- 使用 `SynchronousQueue`（同步队列）

适合：
- 大量短期异步任务、高并发场景
- 非常容易创建过多线程 → OOM

---

### 1.3 `newSingleThreadExecutor()`
创建一个**单线程线程池**（始终只有一个线程）。

```java
ExecutorService pool = Executors.newSingleThreadExecutor();
```

特点：
- 核心线程数 = 最大线程数 = 1
- 保证任务顺序执行（FIFO）
- 使用 `LinkedBlockingQueue`（无界队列）

适合：
- 串行任务、不希望并发执行的任务

---

### 1.4 `newScheduledThreadPool(int corePoolSize)`
创建一个**可定时或周期执行任务**的线程池。

```java
ScheduledExecutorService pool = Executors.newScheduledThreadPool(3);
```

常用方法：
```java
pool.schedule(() -> doSomething(), 5, TimeUnit.SECONDS);
pool.scheduleAtFixedRate(task, 1, 3, TimeUnit.SECONDS); // 每3秒一次
```

---

### 1.5 `newSingleThreadScheduledExecutor()`
单线程版的定时线程池。

---

