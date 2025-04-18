# LockSupport


`LockSupport` 是 JDK 提供的一个用于**创建锁和其他同步类的基础工具类**。

它主要提供两个方法：

- `park()`：**挂起线程**
- `unpark(Thread t)`：**唤醒线程**

相比 `wait/notify`，它不依赖对象的锁；
相比 `Thread.sleep()`，它可以提前被唤醒；
相比 `Condition`，它更底层、灵活。

---


## 1. 基本使用示例

```java
public class LockSupportDemo {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            System.out.println("线程开始执行...");
            LockSupport.park(); // 阻塞自己
            System.out.println("线程被唤醒！");
        });

        t.start();
        Thread.sleep(2000); // 主线程睡2秒

        System.out.println("主线程准备唤醒子线程...");
        LockSupport.unpark(t); // 唤醒子线程
    }
}
```

输出：

```
线程开始执行...
主线程准备唤醒子线程...
线程被唤醒！
```

---

## 2. 和 Object.wait() 有何不同

| 对比项 | LockSupport | Object.wait() |
|--------|---------|-----------|
| 是否需要持有锁 | 不需要 | 需要 |
| 唤醒机制 | 指定线程 unpark | 唤醒某个或全部等待线程 |
| 可否先唤醒再阻塞 | 可以先 unpark，再 park（不会阻塞） | 必须先 wait，再 notify |
| 是否支持中断 | 可中断 | 可中断 |
| 底层实现 | Unsafe + CAS | JVM 内置 monitor |

---




