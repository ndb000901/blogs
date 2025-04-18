# CyclicBarrier

## 1. CyclicBarrier 

`CyclicBarrier` 是 Java 并发包 `java.util.concurrent` 中的一个同步辅助类，用于让一组线程互相等待，**直到达到一个共同的“屏障点”**。

通俗来说就是：**“等人齐了再一起出发”**。

---

## 2. 基本使用

```java
CyclicBarrier barrier = new CyclicBarrier(3);

Runnable task = () -> {
    try {
        System.out.println(Thread.currentThread().getName() + " 到达屏障");
        barrier.await();  // 等待其他线程
        System.out.println(Thread.currentThread().getName() + " 冲破屏障");
    } catch (Exception e) {
        e.printStackTrace();
    }
};

new Thread(task).start();
new Thread(task).start();
new Thread(task).start();
```

### 输出：
```
Thread-0 到达屏障
Thread-1 到达屏障
Thread-2 到达屏障
Thread-2 冲破屏障
Thread-1 冲破屏障
Thread-0 冲破屏障
```

> 等到 3 个线程都 `await()`，屏障才放行！

---

## 3. 关键特性

| 特性 | 说明 |
|------|------|
| 可重用 | 线程到达后自动释放，**可以重复使用**（不同于 CountDownLatch） |
| 屏障点 | 所有线程都执行 `await()` 才放行 |
| 可设回调 | 可以指定一个 barrierAction，在最后一个线程到达时执行一次 |
| 可中断 | `await()` 会抛出异常响应中断或超时 |

---

## 4. 带 barrierAction 的例子

```java
CyclicBarrier barrier = new CyclicBarrier(3, () -> {
    System.out.println("人齐了，开始下一阶段！");
});
```

这个 `Runnable` 是 **“最后一个到达线程”负责执行的回调**。

---


---

## 5. 常见问题点

1. **CyclicBarrier 可重用吗？**
    - 是的，屏障释放后可以重置用于下一轮；

2. **屏障被打破怎么办？**
    - 某线程中断或超时，屏障会 `BrokenBarrierException`；

3. **await 可以超时吗？**
    - 有超时重载：`barrier.await(timeout, TimeUnit.SECONDS);`

4. **最后一个线程执行回调是谁？**
    - 是最后调用 `await()` 成功的线程（线程不固定）。

---





