# 工具-jmap

`jmap` 是 JDK 提供的一个强大的内存分析工具，主要用于：
- **获取 Java 堆内存使用情况**
- **生成 Java 堆转储（Heap Dump）**
- **查看类加载信息**
- **分析 `PermGen` 或 `Metaspace`（JDK 8+）**

适用于排查 **OOM（内存溢出）、类加载泄露、对象分布** 等问题。

---

## **1. 用法**
```bash

jmap [options] <pid>
```
- `<pid>`：Java 进程 ID，可以用 `jps` 获取。
- `[options]`：`jmap` 提供的不同功能。

---

## **2. 常用选项**
### **2.1 查看堆内存使用情况**
```bash

jmap -heap <pid>
```
**作用**：显示堆内存分布、GC 统计、堆的使用情况。

**示例**
```bash

jmap -heap 12345
```
**示例输出**
```
Heap Configuration:
   MinHeapFreeRatio         = 40
   MaxHeapFreeRatio         = 70
   MaxHeapSize              = 4294967296 (4096MB)
   NewSize                  = 268435456 (256MB)
   MaxNewSize               = 1431306240 (1365MB)
   OldSize                  = 536870912 (512MB)
   MetaspaceSize            = 21807104 (20MB)
   CompressedClassSpaceSize = 1073741824 (1024MB)

Heap Usage:
New Generation (Eden + 1 Survivor Space):
   Eden Space 512.0MB,  90% used
   From Space 64.0MB,  50% used
   To   Space 64.0MB,   0% used
Old Generation:
   2.0GB,  75% used
Metaspace:
   50MB,  60% used
```
**用途**
- **排查 OOM**：如果 `Old Generation` 已满，则可能是对象未被回收，导致 `OutOfMemoryError`。
- **检查 GC 效果**：观察 `Eden` 和 `Old` 区使用率是否过高，判断是否需要 GC 调优。

---

### **2.2 列出堆中的对象统计信息**
```bash

jmap -histo <pid>
```
**作用**：按**对象类型**列出 Java 堆中的对象实例数量及大小，可用于**分析内存泄露**。

**示例**
```bash

jmap -histo 12345 | head -20
```
**示例输出**
```
 num    #instances    #bytes  class name
----------------------------------------------
   1:      123456   98765432  [B  (byte数组)
   2:      54321    5432100   java.lang.String
   3:      32100    1284000   java.util.HashMap$Node
   4:      21000     840000   java.lang.Integer
```
**用途**
- **排查内存泄漏**：
  - 如果某个类的实例数量异常增多（如 `HashMap`、`byte[]`），可能存在**缓存未释放**的问题。
- **分析大对象**：
  - `byte[]` 大量占用内存，可能是**大文件加载**或**网络数据缓冲**导致的。

---

### **2.3 生成堆转储文件（Heap Dump）**
```bash

jmap -dump:format=b,file=<dump-file> <pid>
```
**作用**：将 JVM 内存快照保存到文件，以便后续使用 `MAT`（Eclipse Memory Analyzer）或 `jvisualvm` 进行分析。

**示例**
```bash

jmap -dump:format=b,file=/tmp/heap_dump.hprof 12345
```
**用途**
- **分析 OOM**：
  - 生成 `hprof` 文件后，用 `MAT` 分析**哪个对象占用最多内存**，找出内存泄漏的元凶。
- **监测内存增长**：
  - 多次 dump 进行对比，查看哪些对象数量异常增长。

**注意**
- 生成 Dump 可能导致应用短暂停滞（STW）。
- `JDK 8+` 可以用 `jcmd` 替代：
  ```bash
  jcmd <pid> GC.heap_dump filename=/tmp/heap_dump.hprof
  ```

---

### **2.4 查看类加载信息**
```bash

jmap -clstats <pid>
```
**作用**：显示 `ClassLoader` 统计信息，如已加载的类、占用的 Metaspace 空间。

**示例**
```bash

jmap -clstats 12345
```
**示例输出**
```
Loaded classes:  10234
Unloaded classes: 500
Heap usage for class metadata: 40MB
```
**用途**
- **监测类加载泄漏**：
  - 如果 `Loaded classes` 持续增长，可能是 `ClassLoader` 没有正确释放，导致 `Metaspace OOM`。
- **优化 Metaspace 大小**：
  - 观察 `Heap usage for class metadata`，判断是否需要调整 `-XX:MetaspaceSize`。

---

### **(5) 列出堆中的对象（包含 GC 信息）**
```bash

jmap -finalizerinfo <pid>
```
**作用**：列出**等待 `finalize()` 处理**的对象，通常表示对象尚未被 GC 释放，可能有**资源泄露**。

**示例**
```bash

jmap -finalizerinfo 12345
```
**用途**
- **找出未正确关闭的资源**：
  - `InputStream` / `Socket` / `File` 如果仍在 `finalizer` 队列中，可能存在**资源泄露**。

