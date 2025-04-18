# ScheduledThreadPoolExecutor


`ScheduledThreadPoolExecutor` 是 JDK 中用于**定时任务调度**的线程池，继承自 `ThreadPoolExecutor`，实现了 `ScheduledExecutorService` 接口。

它可以用来实现：
- **延迟执行**
- **固定频率周期执行**
- **固定延迟周期执行**

底层是一个可调度的线程池，任务调度通过 `DelayedWorkQueue` 来实现。

---

## 1. 基本用法

```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);
```

也可以直接使用构造函数：
```java
ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(2);
```

---

## 2. 常用 API 示例

### 2.1 `schedule(Runnable command, long delay, TimeUnit unit)`
延迟执行一次任务：
```java
scheduler.schedule(() -> {
    System.out.println("执行一次任务：" + System.currentTimeMillis());
}, 5, TimeUnit.SECONDS);
```

---

### 2.2 `scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)`
**固定频率执行**（无论任务执行时间长短，周期固定）：

```java
scheduler.scheduleAtFixedRate(() -> {
    System.out.println("fixedRate: " + System.currentTimeMillis());
}, 2, 3, TimeUnit.SECONDS);
```

特点：
- 第一次延迟 `initialDelay` 执行
- 之后每隔 `period` 时间执行一次
- 如果任务执行时间超过周期时间，下一次会**立刻执行**

---

### 2.3 `scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit)`
**固定延迟执行**（每次任务完成后，延迟固定时间再执行下一次）：

```java
scheduler.scheduleWithFixedDelay(() -> {
    System.out.println("fixedDelay: " + System.currentTimeMillis());
}, 2, 3, TimeUnit.SECONDS);
```

特点：
- 上一个任务执行完 → 等 `delay` 秒再执行下一个
- 适合任务执行时间不稳定的情况

---


