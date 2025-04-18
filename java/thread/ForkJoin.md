# Fork/Join 框架


`Fork/Join` 框架的本质是将**一个大任务拆分成多个小任务**并行处理，最后再将多个小任务的结果**合并**起来得到最终结果。

## 1. 应用场景：
适合处理 **可以递归拆分成子任务** 的计算密集型任务，如：
- 大数组求和
- 快速排序
- 文件递归处理
- 图像处理

---

## 2. 核心类结构

```text
java.util.concurrent
├── ForkJoinPool       // 线程池
├── ForkJoinTask<V>    // 抽象任务类
│   ├── RecursiveTask<V>   // 有返回值的任务
│   └── RecursiveAction    // 无返回值的任务
```

---

## 3. 基本使用方式

### 3.1 使用 RecursiveTask（有返回值）

```java
class SumTask extends RecursiveTask<Long> {
    private static final int THRESHOLD = 10000;
    private long start, end;

    public SumTask(long start, long end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        if (end - start <= THRESHOLD) {
            // 不再拆分，直接计算
            long sum = 0;
            for (long i = start; i <= end; i++) sum += i;
            return sum;
        } else {
            // 拆分
            long middle = (start + end) / 2;
            SumTask left = new SumTask(start, middle);
            SumTask right = new SumTask(middle + 1, end);
            left.fork();             // 异步执行左边任务
            long rightResult = right.compute(); // 当前线程执行右边
            long leftResult = left.join();      // 等待左边结果
            return leftResult + rightResult;
        }
    }
}
```

### 3.2 启动任务

```java
public static void main(String[] args) {
    ForkJoinPool pool = new ForkJoinPool(); // 默认CPU核数线程
    SumTask task = new SumTask(1, 1_000_000);
    long result = pool.invoke(task);
    System.out.println("结果: " + result);
}
```

---

## 4. 任务方法详解

| 方法名     | 含义 |
|------------|------|
| `fork()`   | 异步提交子任务 |
| `join()`   | 等待子任务完成并返回结果 |
| `invoke()` | 提交并等待返回结果（同步） |
| `compute()`| 任务的核心计算逻辑 |

> 注意：推荐 **一个任务 fork 后，当前线程执行另一个子任务**，效率高。

---

## 5. 工作窃取（Work Stealing）

ForkJoinPool 背后的算法是 **工作窃取（Work Stealing Algorithm）**：

- 每个线程维护一个双端队列（Deque）；
- 正常任务从头部出；
- 其他线程**窃取**任务时从尾部取，减少竞争。

---

## 6. ForkJoinPool 参数说明

```java
new ForkJoinPool(
    int parallelism,                 // 并行度（推荐为 CPU 核数）
    ForkJoinWorkerThreadFactory factory,
    UncaughtExceptionHandler handler,
    boolean asyncMode                // true=FIFO；false=LIFO（默认）
)
```

---

## 7. 注意事项

| 项目 | 说明 |
|------|------|
| 阈值控制 | 拆分过细会降低性能，应合理设置 `THRESHOLD` |
| IO任务不适合 | 适合 CPU 密集型任务，不适合 IO 阻塞任务 |
| 不建议在 ForkJoinTask 内使用 `Thread.sleep()` |
| 递归不可无限 | 否则可能堆栈溢出，务必设置拆分终止条件 |

---

