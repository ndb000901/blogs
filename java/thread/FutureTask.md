# FutureTask

`FutureTask` 是 Java 并发包中用于表示**可取消的异步任务**，它实现了两个重要接口：

- `Runnable`：可以被线程或线程池执行
- `Future<V>`：可以获取任务结果、判断是否完成、取消任务等

也就是说，`FutureTask` 既是一个任务，也是一个结果容器。

---

## 1. 使用场景

### 用于异步执行任务并获取结果：
```java
Callable<Integer> callable = () -> {
    Thread.sleep(1000);
    return 42;
};

FutureTask<Integer> futureTask = new FutureTask<>(callable);
new Thread(futureTask).start();

System.out.println("任务执行中...");
Integer result = futureTask.get(); // 阻塞直到结果返回
System.out.println("结果是：" + result);
```

---

## 2. 构造方法

```java
FutureTask(Callable<V> callable)
FutureTask(Runnable runnable, V result)
```

- 第一个适用于需要有返回值的任务（推荐）
- 第二个用于兼容 `Runnable` 接口（固定返回结果）

---

## 3. 常用方法解析

| 方法                  | 作用说明 |
|-----------------------|----------|
| `get()`               | 阻塞等待结果返回 |
| `get(timeout, unit)`  | 限时等待结果 |
| `isDone()`            | 判断任务是否完成 |
| `isCancelled()`       | 判断是否被取消 |
| `cancel(boolean mayInterruptIfRunning)` | 取消任务执行 |

---

## 4. 源码关键逻辑

### 4.1 内部状态机

```java
private volatile int state;
```

状态值枚举：
- `NEW`：初始状态
- `COMPLETING`：任务正在设置结果
- `NORMAL`：正常完成
- `EXCEPTIONAL`：抛出异常
- `CANCELLED`：被取消
- `INTERRUPTING`、`INTERRUPTED`：处理中断

---

### 4.2 执行入口

#### 实现了 `Runnable` 接口的 `run()` 方法：

```java
public void run() {
    if (state != NEW || !UNSAFE.compareAndSwapObject(...)) {
        return;
    }
    try {
        V result = callable.call();
        set(result); // 设置结果
    } catch (Throwable ex) {
        setException(ex); // 设置异常
    }
}
```

说明：
- 线程执行会调用 `run()`，内部调用 `Callable.call()` 获取结果
- 成功则设置结果并修改状态为 `NORMAL`
- 出现异常则状态改为 `EXCEPTIONAL`

---

### 4.3 获取结果 `get()` 方法

```java
public V get() throws InterruptedException, ExecutionException {
    int s = state;
    if (s <= COMPLETING) {
        awaitDone(false, 0L); // 阻塞等待
    }
    return report(s);
}
```

- 若任务还没完成就会阻塞等待
- 一旦状态变为 `NORMAL` 或 `EXCEPTIONAL`，就返回结果或抛出异常

---

### 4.4 取消任务

```java
public boolean cancel(boolean mayInterruptIfRunning) {
    if (state != NEW) return false;
    if (mayInterruptIfRunning) {
        // 中断线程
    }
    // 状态改为 CANCELLED 或 INTERRUPTED
}
```

说明：
- 只能取消还未开始的或正在执行的任务
- 若任务已完成，取消无效

---

