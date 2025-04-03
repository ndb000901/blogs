# 工具-jstack

`jstack` 是 JDK 提供的 **线程堆栈分析工具**，用于**查看 Java 进程中各线程的调用栈**，帮助排查**死锁、线程阻塞、CPU 过高、程序卡顿**等问题。

---

## **1. 用法**
```bash

jstack [options] <pid>
```
- `<pid>`：目标 Java 进程的 PID（可用 `jps` 获取）。
- `[options]`：可选参数，如 `-F` 强制输出、`-l` 显示锁信息等。

---

## **2. 常用选项**
| 选项 | 作用 |
|------|------|
| `-l` | 显示锁信息（锁对象的哈希码），用于分析死锁 |
| `-F` | 强制输出线程堆栈（适用于 `jstack` 无法正常输出时） |
| `-m` | 显示**本地方法**（Native Stack），查看 JNI 调用 |
| `-e` | 显示所有线程，包括 daemon 线程 |
| `-h` | 显示帮助信息 |

---

## **3. 示例**

### **3.1 查看指定 Java 进程的线程栈**
```bash

jstack <pid>
```
示例：
```bash

jstack 12345
```
输出示例：
```
"main" #1 prio=5 os_prio=0 tid=0x00007fcd0400b800 nid=0x2b83 runnable [0x00007fccfbfff000]
   java.lang.Thread.State: RUNNABLE
        at com.example.Main.main(Main.java:10)
```
**含义解析**
- `"main"`：线程名（主线程）。
- `#1`：线程 ID。
- `prio=5`：Java 线程优先级。
- `os_prio=0`：操作系统级别的优先级。
- `tid=0x00007fcd0400b800`：线程 ID。
- `nid=0x2b83`：线程在 OS 层面的 ID（可用 `top -H -p <pid>` 对比）。
- `RUNNABLE`：线程状态（其他可能状态：`WAITING`、`BLOCKED`、`TIMED_WAITING` 等）。
- `com.example.Main.main(Main.java:10)`：当前运行的代码位置。

---

### **3.2 显示锁信息**
```bash

jstack -l <pid>
```
**用于分析死锁或长时间等待的线程**，示例：
```
Found one Java-level deadlock:
=============================
"Thread-1":
  waiting to lock monitor 0x000000001c009cd8 (object 0x000000076b72bcd8, a java.lang.String),
  which is held by "Thread-2"
"Thread-2":
  waiting to lock monitor 0x000000001c009d28 (object 0x000000076b72bd28, a java.lang.String),
  which is held by "Thread-1"
```
**含义**
- **`Thread-1` 等待 `Thread-2` 持有的锁**，而 **`Thread-2` 也在等待 `Thread-1` 持有的锁**，形成死锁。

---

### **3.3 强制输出线程栈**
```bash

jstack -F <pid>
```
**适用于 `jstack <pid>` 卡住或进程无响应的情况**。

---

### **3.4 显示 Native 调用**
```bash

jstack -m <pid>
```
**用于分析 JNI 调用、C/C++ 代码引起的线程问题**。
```
"main" #1 prio=5 os_prio=0 tid=0x00007fd0a400b800 nid=0x1f23 runnable [0x00007fcfa3fff000]
   java.lang.Thread.State: RUNNABLE
        at java.lang.Thread.sleep(Native Method)
        at com.example.Main.main(Main.java:10)
```
**含义**
- `Native Method` 表示 `sleep()` 是 JNI 调用的本地方法。

---

## **4. 线程状态详解**
| **状态** | **含义** | **典型原因** |
|----------|----------|-------------|
| `RUNNABLE` | 运行中 | 线程占用 CPU 或等待调度 |
| `WAITING` | 无限等待 | `Object.wait()`、`LockSupport.park()` |
| `TIMED_WAITING` | 超时等待 | `Thread.sleep()`、`wait(timeout)` |
| `BLOCKED` | 被锁阻塞 | 线程尝试获取锁但未成功 |
| `TERMINATED` | 已终止 | 线程执行完成 |

---

## **5. 典型问题分析**
### **5.1 CPU 过高**
#### **问题**
当某个 Java 进程的 CPU 使用率过高，可能是某个线程占用大量 CPU 资源。

#### **解决方案**
1. **找到 PID**
   ```bash
   jps -l
   ```
2. **查看 CPU 最高的线程**
   ```bash
   top -H -p <pid>
   ```
3. **转换 `top` 线程 ID（十进制转十六进制）**
   ```bash
   printf "%x\n" 12345
   ```
4. **使用 `jstack` 分析该线程**
   ```bash
   jstack <pid> | grep -A 30 "<nid=0x3039>"
   ```

---

### **5.2 Java 线程死锁**
#### **问题**
多个线程互相等待对方释放锁，导致线程无法继续执行。

#### **解决方案**
```bash

jstack -l <pid>
```
如果发现 `"Found one Java-level deadlock"`，说明存在死锁，需要：
1. **优化锁的使用顺序，避免环形等待**。
2. **使用 `tryLock()` 设置超时时间**，避免无限等待。
3. **减少锁的粒度，尽量使用 `ReentrantLock`**。

---

### **5.3 线程长时间阻塞**
#### **问题**
应用程序无响应，可能是某个线程长期处于 `BLOCKED` 状态。

#### **解决方案**
1. **使用 `jstack -l <pid>`**
2. **查看 `BLOCKED` 线程的调用栈**
3. **优化同步逻辑，避免长时间锁等待**


