# CountDownLatch

`CountDownLatch` 是 Java 并发包中的一个非常常用的同步工具类，用于**线程间的协调**，常见于“一等多”或者“多等一”的场景。

---

## 1. CountDownLatch 

它允许一个或多个线程等待，直到在其他线程中执行的一组操作完成为止。

来自包：
```java
java.util.concurrent.CountDownLatch
```

---

## 2. 核心构造与方法

```java
CountDownLatch latch = new CountDownLatch(3); // 初始化计数为 3
```

### 常用方法：

| 方法                    | 说明 |
|-------------------------|------|
| `await()`               | 当前线程等待直到计数为 0 |
| `countDown()`           | 将计数减 1（原子操作） |
| `await(long time, TimeUnit unit)` | 最多等待一定时间 |

---

## 3. CountDownLatch 举例

---

### 场景1：主线程等待多个子线程完成

```java
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        int threadCount = 3;
        CountDownLatch latch = new CountDownLatch(threadCount);

        for (int i = 0; i < threadCount; i++) {
            final int id = i;
            new Thread(() -> {
                try {
                    System.out.println("子线程 " + id + " 开始工作");
                    Thread.sleep((long)(Math.random() * 2000));
                    System.out.println("子线程 " + id + " 完成工作");
                } catch (InterruptedException ignored) {}
                latch.countDown(); // 每个线程工作完，计数减1
            }).start();
        }

        latch.await(); // 主线程等待
        System.out.println("所有子线程工作完成，主线程继续");
    }
}
```

---

### 场景2：多个线程等待统一起跑（模拟并发）

```java
public class StartTogether {
    public static void main(String[] args) throws InterruptedException {
        int n = 5;
        CountDownLatch startGate = new CountDownLatch(1); // 起跑门
        CountDownLatch endGate = new CountDownLatch(n);   // 终点门

        for (int i = 0; i < n; i++) {
            new Thread(() -> {
                try {
                    startGate.await(); // 等待开始命令
                    System.out.println(Thread.currentThread().getName() + " 开始执行");
                    Thread.sleep((long)(Math.random() * 1000));
                } catch (InterruptedException ignored) {}
                endGate.countDown(); // 线程完成
            }).start();
        }

        System.out.println("准备开始...");
        Thread.sleep(1000);
        startGate.countDown(); // 发令
        endGate.await();       // 等所有线程结束
        System.out.println("所有线程已完成");
    }
}
```

---

## 3. CountDownLatch 特点

| 特性           | 说明                                     |
|----------------|------------------------------------------|
| 一次性         | **计数器为 0 后就不能重置**              |
| 初始计数       | 通过构造函数设定                         |
| 原子性         | `countDown()` 是线程安全的原子操作       |
| 用完就废       | 想复用请使用 `CyclicBarrier` 或 `Semaphore` |

---

## 4. 与其它同步工具对比

| 工具            | 是否可重用 | 支持阻塞线程数 | 用途                       |
|-----------------|-------|----------------|----------------------------|
| `CountDownLatch` | 不可重用 | 任意个         | 等待多个任务完成           |
| `CyclicBarrier`  | 可循环使用 | 固定数量       | 所有线程都准备好才继续     |
| `Semaphore`      | 可复用 | 动态许可控制   | 控制资源访问数量（限流）   |
| `Phaser`         | 强大的多阶段栅栏 | 支持动态线程注册 | 复杂的多阶段线程协作       |

---



