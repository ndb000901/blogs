# Python基础-多线程


多线程（Multithreading）是程序并行执行的一种方式，允许同时执行多个线程，每个线程都执行程序的一部分。Python 中的多线程通常用于提高 I/O 密集型任务的性能，例如网络请求、文件读写等，因为 Python 使用全局解释器锁（GIL），限制了多线程在 CPU 密集型任务中的优势。

在 Python 中，使用 `threading` 模块来实现多线程。

---

## 1. 线程

### **线程（Thread）**
线程是程序中执行的最小单位。每个线程都有自己的执行流，与其他线程并发运行。线程共享进程中的资源（如内存、文件句柄等）。

### **多线程**
多线程指的是在同一个进程中并发执行多个线程。多线程可以提高程序的执行效率，尤其在 I/O 密集型应用中更为有效。

### **Python 中的线程**
Python 中的线程由 `threading` 模块提供支持。由于 Python 的 GIL，线程并不是在多个 CPU 核心上真正并行执行，而是交替执行（特别是在 CPU 密集型任务时）。

---

## 2. 线程实现

### 2.1 **创建线程**

Python 中通过 `threading.Thread` 类来创建线程。线程可以通过继承 `Thread` 类并重写 `run()` 方法来实现，或者通过传递目标函数给线程来实现。

#### **通过继承 Thread 类创建线程**

```python
import threading
import time

# 定义一个线程类，继承自 Thread
class MyThread(threading.Thread):
    def run(self):
        print(f"Thread {threading.current_thread().name} is running")
        time.sleep(1)
        print(f"Thread {threading.current_thread().name} finished")

# 创建线程实例
thread1 = MyThread()
thread2 = MyThread()

# 启动线程
thread1.start()
thread2.start()

# 等待线程结束
thread1.join()
thread2.join()

print("All threads finished")
```

####  **通过目标函数创建线程**

```python
import threading
import time

# 定义目标函数
def thread_task():
    print(f"Thread {threading.current_thread().name} is running")
    time.sleep(1)
    print(f"Thread {threading.current_thread().name} finished")

# 创建线程实例
thread1 = threading.Thread(target=thread_task)
thread2 = threading.Thread(target=thread_task)

# 启动线程
thread1.start()
thread2.start()

# 等待线程结束
thread1.join()
thread2.join()

print("All threads finished")
```

在以上代码中：
- `start()` 启动线程，`run()` 方法是线程的实际执行函数。
- `join()` 用于等待线程完成。

---

## 3. 线程同步与共享资源

在多线程程序中，多个线程可能会访问共享资源（如变量、文件等）。如果多个线程同时对资源进行修改，可能会导致数据不一致的情况。为了解决这个问题，Python 提供了同步机制。

### 3.1 **线程同步**

#### **锁（Lock）**
锁是一种最常见的同步机制，确保同一时间只有一个线程访问共享资源。

```python
import threading

# 创建一个锁
lock = threading.Lock()

# 共享资源
counter = 0

def thread_task():
    global counter
    for _ in range(1000):
        # 获取锁
        with lock:
            counter += 1

# 创建线程实例
threads = [threading.Thread(target=thread_task) for _ in range(10)]

# 启动线程
for thread in threads:
    thread.start()

# 等待线程结束
for thread in threads:
    thread.join()

print(f"Final counter value: {counter}")
```

在上面的代码中，`lock` 确保每次只有一个线程能够修改 `counter` 变量。`with lock` 是一个上下文管理器，能够自动获取和释放锁。

####  **互斥锁（Mutex）与递归锁（RLock）**
- **Mutex（互斥锁）** 是锁的一个特殊类型，适用于防止多线程访问共享资源。Python 中的 `Lock` 实际上是互斥锁。
- **RLock（递归锁）** 是一种锁，可以被同一个线程多次获取，用于避免死锁。

```python
# 使用 RLock
rlock = threading.RLock()

def thread_task():
    with rlock:
        print(f"Thread {threading.current_thread().name} acquired the lock")
        # do something

# 创建线程实例
threads = [threading.Thread(target=thread_task) for _ in range(5)]

# 启动线程
for thread in threads:
    thread.start()

# 等待线程结束
for thread in threads:
    thread.join()
```

#### **条件变量（Condition）**
条件变量用于多个线程之间的通知和等待机制。例如，在生产者-消费者模型中，消费者需要等待生产者生产数据。

```python
import threading
import time

condition = threading.Condition()

# 生产者线程
def producer():
    with condition:
        print("Producer: producing item")
        time.sleep(1)
        condition.notify()  # 通知消费者

# 消费者线程
def consumer():
    with condition:
        print("Consumer: waiting for item")
        condition.wait()  # 等待生产者通知
        print("Consumer: item consumed")

# 创建线程实例
producer_thread = threading.Thread(target=producer)
consumer_thread = threading.Thread(target=consumer)

# 启动线程
consumer_thread.start()
producer_thread.start()

# 等待线程结束
producer_thread.join()
consumer_thread.join()
```

---

## 4. 线程池

Python 的 `concurrent.futures` 模块提供了 `ThreadPoolExecutor`，用于更方便地管理线程池。

### 4.1 **线程池**

线程池是一种线程复用的机制，可以避免频繁地创建和销毁线程，提高程序性能。

```python
from concurrent.futures import ThreadPoolExecutor

def thread_task(i):
    print(f"Thread {i} is running")

# 创建线程池
with ThreadPoolExecutor(max_workers=3) as executor:
    for i in range(5):
        executor.submit(thread_task, i)
```

在这个例子中，线程池最大并发数为 3，最多有 3 个线程同时工作。

---
