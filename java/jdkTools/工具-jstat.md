# 工具-jstat

`jstat`（JVM Statistics Monitoring Tool）是 JDK 自带的 Java 统计监控工具，它用于监视 Java 虚拟机（JVM）的运行状态，特别是 **GC（垃圾回收）**、**类加载**、**JIT 编译** 等信息。

---

## **1. `jstat` 的基本用法**
```sh

jstat [option] <vmid> [interval] [count]
```
- `<vmid>`：目标 Java 进程的 PID，可以用 `jps` 查询。
- `[interval]`（可选）：刷新间隔（毫秒）。
- `[count]`（可选）：输出次数。

**示例**：
```sh

jstat -gc 12345 1000 10
```
每 **1 秒** 监控一次 `PID 12345` 的 GC 状态，持续 **10 次**。

---

## **2.选项**
`jstat` 提供多个监控选项，主要用于 **GC、类加载、JIT 编译** 等。

可使用以下命令查询
```bash

jstat -options
```

| 选项      | 作用 |
|-----------|------|
| `-class`  | 监控类加载情况 |
| `-compiler` | 监控 JIT 编译情况 |
| `-gc` | 监控 GC 概览（堆内存使用情况） |
| `-gccapacity` | 监控 GC 各区域最大容量 |
| `-gcutil` | 监控 GC 各区域使用率（推荐） |
| `-gccause` | 监控 GC 触发原因 |
| `-gcnew` | 监控新生代 GC |
| `-gcold` | 监控老年代 GC |
| `-gcpermcapacity` | 监控永久代（JDK 8 以前）或 Metaspace（JDK 8+） |
| `-printcompilation` | 监控 JIT 编译的方法 |

---

## **3. 详细示例**
### **3.1 监控 GC 概览**
```sh

jstat -gc 12345 1000 5
```
**示例输出**：
```
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC      MU     CCSC   CCSU     YGC    YGCT   FGC   FGCT    GCT
 512.0  512.0   0.0    0.0   4096.0   1024.0   8192.0     2048.0    1024.0   512.0   128.0   64.0       5   0.123     1   0.456   0.579
```
**字段解析**：
- **S0C, S1C, S0U, S1U**：Survivor 0/1 区的容量/使用率。
- **EC, EU**：Eden 区容量/使用率。
- **OC, OU**：Old 区（老年代）容量/使用率。
- **MC, MU**：元空间（Metaspace）容量/使用率。
- **CCSC, CCSU**：Class Metaspace 容量/使用率。
- **YGC, YGCT**：Minor GC 次数 / 时间。
- **FGC, FGCT**：Full GC 次数 / 时间。
- **GCT**：GC 总时间。

时间默认是秒，内存默认kb

---

### **3.2 监控 GC 各区域使用率（推荐）**
```sh

jstat -gcutil 12345 1000 5
```
**示例输出**：
```
  S0     S1     E      O      M     CCS   YGC     YGCT    FGC    FGCT     GCT
  0.00  23.45  65.12  80.34  90.12  70.34   5     0.123     1    0.456   0.579
```
- 这比 `-gc` 更直观，**直接给出使用率百分比**（`0~100%`）。

---

### **3.3 监控 GC 触发原因**
```sh

jstat -gccause 12345
```
**示例输出**：
```
GCCause        LGCCause
System.gc()    Allocation Failure
```
- `GCCause`：最近一次 GC 触发原因。
- `LGCCause`：上一次 GC 触发原因。

---

### **3.4 监控类加载**
```sh

jstat -class 12345 1000 5
```
**示例输出**：
```
Loaded  Bytes  Unloaded  Bytes    Time
  12345  56789      23     1024   1.234
```
- **Loaded**：已加载类的数量。
- **Unloaded**：已卸载类的数量。
- **Time**：类加载所耗时间。
- **Bytes**: kb

---

### **3.5 监控 JIT 编译**
```sh

jstat -compiler 12345
```
**示例输出**：
```
Compiled   Failed   Invalid   Time   FailedType
  12345        0        10    2.345       0
```
- **Compiled**：已编译的方法数量。
- **Failed**：编译失败的次数。
- **Invalid**：JIT 失效的次数。

---

### **3.6 监控 JIT 编译的方法**
```sh

jstat -printcompilation 12345
```
**示例输出**：
```
Compiled  Size  Type  Method
12345     120   C1    com.example.MyClass::myMethod
```
- **C1**：表示 **C1（Client Compiler）** 编译。
- **C2**：表示 **C2（Server Compiler）** 编译。

---


