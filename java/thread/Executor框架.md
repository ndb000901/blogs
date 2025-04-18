# Executor 框架

Java 提供的用于**管理和调度线程的统一框架**，它将任务的**提交**与任务的**执行机制**分离开来，大大简化多线程编程。

核心思想是：
> 把任务提交给线程池，由线程池决定如何执行任务，而不是程序员手动创建线程。

---

## 1. 架构图

```
+----------------+
|    Executor    |  -- 顶层接口（执行任务）
+----------------+
        |
        v
+----------------------+
|   ExecutorService    |  -- 扩展接口（支持生命周期控制、任务提交、结果获取）
+----------------------+
        |
        v
+-----------------------------+
|   ThreadPoolExecutor        |  -- 线程池的核心实现类
+-----------------------------+
```

---

## 2. 核心接口与类说明

| 名称 | 说明 |
|------|------|
| `Executor` | 顶层接口，定义了 `execute(Runnable)` 方法 |
| `ExecutorService` | 扩展接口，支持关闭、提交任务、有返回值任务 |
| `ScheduledExecutorService` | 扩展接口，支持定时任务和周期任务 |
| `ThreadPoolExecutor` | 最常用的线程池实现类 |
| `Executors` | 工具类，提供了创建线程池的静态方法 |

---

## 3. 常用线程池创建方式（Executors）

```java
// 1. 固定大小线程池
ExecutorService pool1 = Executors.newFixedThreadPool(5);

// 2. 单线程线程池（串行执行）
ExecutorService pool2 = Executors.newSingleThreadExecutor();

// 3. 可缓存线程池（适合短任务、高并发）
ExecutorService pool3 = Executors.newCachedThreadPool();

// 4. 定时线程池
ScheduledExecutorService pool4 = Executors.newScheduledThreadPool(3);
```

注意：**建议不要使用 Executors 直接创建线程池！**

### 推荐使用方式：
```java
ExecutorService pool = new ThreadPoolExecutor(
    2,                 // corePoolSize
    5,                 // maximumPoolSize
    60L,               // keepAliveTime
    TimeUnit.SECONDS,  // unit
    new LinkedBlockingQueue<>(100), // 队列
    Executors.defaultThreadFactory(),  // 线程工厂
    new ThreadPoolExecutor.AbortPolicy() // 拒绝策略
);
```

---

## 4. ThreadPoolExecutor 参数详解

| 参数 | 说明 |
|------|------|
| corePoolSize | 核心线程数 |
| maximumPoolSize | 最大线程数 |
| keepAliveTime | 空闲线程最大存活时间 |
| unit | 时间单位 |
| workQueue | 阻塞队列（如 LinkedBlockingQueue） |
| threadFactory | 线程工厂，决定新线程怎么创建 |
| handler | 拒绝策略（AbortPolicy、CallerRunsPolicy 等） |

---

## 5. 线程池的执行流程

1. 提交任务
2. 如果线程数 < corePoolSize，创建新线程执行任务
3. 否则，尝试将任务加入队列
4. 队列满了，看线程数是否 < maxPoolSize，若是则新建线程
5. 否则执行拒绝策略

---

## 6. 拒绝策略（RejectedExecutionHandler）

| 策略 | 含义 |
|------|------|
| `AbortPolicy` | 直接抛异常（默认） |
| `CallerRunsPolicy` | 谁提交谁执行 |
| `DiscardPolicy` | 直接丢弃任务 |
| `DiscardOldestPolicy` | 丢弃队列最早的任务，尝试提交当前任务 |

---

## 7. 常用方法（ExecutorService）

| 方法 | 说明 |
|------|------|
| `submit(Runnable)` | 提交任务（无返回值） |
| `submit(Callable)` | 提交任务（有返回值） |
| `invokeAll(Collection)` | 同时提交多个任务，阻塞等待结果 |
| `shutdown()` | 关闭线程池，不再接收新任务 |
| `shutdownNow()` | 尝试强制关闭（中断正在执行的任务） |
| `awaitTermination()` | 等待线程池结束 |

---


