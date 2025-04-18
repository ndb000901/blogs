# Semaphore

`Semaphore` 是 `java.util.concurrent` 提供的**信号量机制**，用于控制**同时访问特定资源的线程数量**。

它内部维护一个**许可计数器（permits）**，线程调用 `acquire()` 获取许可，如果没有许可则阻塞等待；调用 `release()` 归还许可。

---

## 2. 基本使用

```java
Semaphore semaphore = new Semaphore(3); // 最多允许3个线程同时访问

Runnable task = () -> {
    try {
        semaphore.acquire(); // 获取许可（坑位）
        System.out.println(Thread.currentThread().getName() + " 获取许可");
        Thread.sleep(1000);
        System.out.println(Thread.currentThread().getName() + " 释放许可");
    } catch (InterruptedException e) {
        e.printStackTrace();
    } finally {
        semaphore.release(); // 释放许可
    }
};

for (int i = 0; i < 10; i++) {
    new Thread(task).start();
}
```

---

## 3. 常用方法

| 方法 | 说明 |
|------|------|
| `acquire()` | 获取一个许可，若无则阻塞 |
| `acquire(int n)` | 获取 n 个许可 |
| `tryAcquire()` | 尝试获取许可，立即返回 true/false |
| `tryAcquire(timeout, unit)` | 在超时时间内尝试获取 |
| `release()` | 释放一个许可 |
| `release(int n)` | 释放 n 个许可 |
| `availablePermits()` | 返回当前可用许可数量 |

---

## 4. 与锁的区别（Lock vs Semaphore）

| 特性 | Lock（互斥锁） | Semaphore（信号量） |
|------|--------------|-------------------|
| 控制并发数量 | 仅1个线程可进入 | 可多个 |
| 用途 | 互斥、保护临界区 | 控流、限流、资源池 |
| 可重入性 | 默认可重入 | 不可重入 |
| 公平性 | 可配置 | 可配置 |

---

## 5. 常见使用场景

### 5.1 控制并发线程数量（限流）

```java
ExecutorService pool = Executors.newCachedThreadPool();
Semaphore semaphore = new Semaphore(5); // 最多5个并发线程

for (int i = 0; i < 100; i++) {
    pool.submit(() -> {
        try {
            semaphore.acquire();
            // 执行业务逻辑
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            semaphore.release();
        }
    });
}
```

### 5.2 数据库连接池、资源池控制

只允许固定线程同时获取资源。

### 5.3 实现“通行证”机制

```java
Semaphore gate = new Semaphore(0); // 初始为0，相当于关着门

new Thread(() -> {
    try {
        gate.acquire(); // 等待“放行”
        System.out.println("通过关卡");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();

// 主线程放行
Thread.sleep(3000);
gate.release(); // 放行一个
```

---


