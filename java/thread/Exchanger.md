# Exchanger

`Exchanger` 是 JDK 提供的一个**用于在线程之间交换数据的同步点**。

本质上是一个**两个线程之间的数据交换器**：
- 线程 A 把数据交给线程 B，
- 同时从线程 B 获取数据（反之亦然）。

它的全名是：`java.util.concurrent.Exchanger<V>`，泛型表示要交换的数据类型。

---


## 1. 基本使用示例

```java
Exchanger<String> exchanger = new Exchanger<>();

Thread t1 = new Thread(() -> {
    try {
        String data = "数据A";
        System.out.println("线程1 准备交换: " + data);
        String result = exchanger.exchange(data); // 等待线程2交换
        System.out.println("线程1 得到: " + result);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

Thread t2 = new Thread(() -> {
    try {
        String data = "数据B";
        System.out.println("线程2 准备交换: " + data);
        String result = exchanger.exchange(data); // 等待线程1交换
        System.out.println("线程2 得到: " + result);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

t1.start();
t2.start();
```

输出可能是：

```
线程1 准备交换: 数据A
线程2 准备交换: 数据B
线程1 得到: 数据B
线程2 得到: 数据A
```

---

## 2. 方法说明

| 方法 | 说明 |
|------|------|
| `V exchange(V x)` | 当前线程把 `x` 交出去，并等待另一个线程也交换 |
| `V exchange(V x, long timeout, TimeUnit unit)` | 设置超时时间，避免永久阻塞 |

---


