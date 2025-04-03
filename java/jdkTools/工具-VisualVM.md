# 工具-VisualVM

`VisualVM` 是 Java 官方提供的 **可视化性能分析工具**，用于 **JVM 监控、性能分析、内存分析、线程分析**，并支持 **Heap Dump、CPU Profiler** 等高级调试功能。  

---

## **1.安装 VisualVM**
### **下载**
- **官网**：[https://visualvm.github.io/](https://visualvm.github.io/)
- **下载 ZIP 版**，解压后运行：
  ```sh
  ./bin/visualvm
  ```

## **2.连接 JVM**
### **本地 JVM 连接**
启动 `VisualVM` 后，左侧面板会自动列出本地运行的 Java 进程（如 `Tomcat`、`Spring Boot` 应用）。
**双击** 目标进程，即可开始监控。

### **远程 JVM 连接**
如果要监控远程服务器上的 JVM：
1. 目标服务器启用 **JMX 远程访问**：
   ```sh
   
   java -Dcom.sun.management.jmxremote \
        -Dcom.sun.management.jmxremote.port=9999 \
        -Dcom.sun.management.jmxremote.rmi.port=9998 \
        -Dcom.sun.management.jmxremote.local.only=false \
        -Dcom.sun.management.jmxremote.authenticate=false \
        -Dcom.sun.management.jmxremote.ssl=false \
        -Djava.rmi.server.hostname=192.168.1.100 \
        -jar yourApp.jar
   ```
2. 在 `VisualVM` 右键 `Remote`，选择 **添加远程主机**，输入 `192.168.1.100:9999`。

---

## **3. VisualVM 主要功能**

### **3.1 概览（Overview）**
显示 JVM 运行状态，包括：
- **JVM 版本**
- **JVM 启动参数**
- **主机 CPU / 内存使用情况**
- **JVM 进程 Uptime（运行时间）**

---

### **3.2 监控（Monitor）**
实时监控 **JVM 资源使用情况**：
- **CPU 使用率**
- **堆内存 / 非堆内存 使用情况**
- **GC 活动**
- **线程数量**

**用途**：
- 查看 **GC 频率**（高频 Full GC 可能是内存泄漏）。
- 观察 **老年代（Old Gen）是否增长**（避免 OOM）。
- 发现 **线程泄漏**（线程数不断增长）。

---

### **3.3 线程（Threads）**
监控 **JVM 线程**：
- 线程 **状态**（RUNNABLE、BLOCKED、WAITING）
- **死锁检测**
- **Dump 线程栈**（类似 `jstack`）

**用途**：
- 查找 **高 CPU 占用线程**
- 检测 **死锁**
- 发现 **线程泄漏**

---

### **3.4 Profiler（CPU 分析）**
`Profiler` 用于 **性能瓶颈分析**：
- 采样 **方法调用次数** 和 **执行时间**
- 找出 **慢方法** 和 **热点代码**
- 查看 **方法调用关系**

**如何使用**：
1. **开启 CPU Profiler**
   - 右键目标 JVM，选择 `CPU Profiler` > `Start`
2. **运行测试**
   - 让应用正常执行一段时间
3. **分析结果**
   - 结束 Profiler，查看 **执行时间最长的方法**
   - 重点优化 **占用 CPU 最高的方法**

---

### **3.5 Heap Dump（内存快照）**
`Heap Dump` 用于 **排查内存泄漏**：
- **对象实例统计**（哪些对象占用最多内存）
- **对象引用关系**（找出未释放的对象）
- **GC Roots 分析**（找出无法回收的对象）

**如何使用**：
1. **手动 Dump**
   - 右键 JVM 进程，选择 `Heap Dump`
2. **分析对象**
   - 在 `Classes` 选项卡中，按 **大小排序**，找出占用最多的对象。
   - 在 `References` 选项卡中，分析 **对象引用链**。

**自动 Heap Dump（防止 OOM）**：
- 在 JVM 启动参数中添加：
  ```sh
  -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/path/to/dump
  ```
  当 OOM 发生时，JVM 自动生成 Heap Dump。

---

### **3.6 GC 分析**
`VisualVM` 提供 **GC 详细分析**：
- **查看 GC 次数、耗时**
- **分析 GC 频率**
- **判断 GC 是否正常回收内存**

**用途**：
- **高频 Full GC**：可能是 **老年代对象增长过快**，需要优化。
- **GC 时间过长**：可能是 **GC 线程数不足**，可调整 `-XX:ParallelGCThreads`。

---

## **4. 远程监控 VisualVM**
如果 `VisualVM` 需要连接 **远程服务器**：

**服务器启用 JMX 远程端口**
   ```sh
   
   java -Dcom.sun.management.jmxremote \
        -Dcom.sun.management.jmxremote.port=9999 \
        -Dcom.sun.management.jmxremote.rmi.port=9998 \
        -Dcom.sun.management.jmxremote.authenticate=false \
        -Dcom.sun.management.jmxremote.ssl=false \
        -Djava.rmi.server.hostname=192.168.1.100 \
        -jar yourApp.jar
   ```

## **5. 插件**

`VisualVM` **插件（Plugins）** 用于扩展其功能，比如 **GC 分析、线程分析、JavaFX 监控** 等。可以通过 **Tools → Plugins** 安装额外的插件。  


### **5.1 安装插件**
1. **打开 `VisualVM`**
2. **菜单栏** → `Tools` → `Plugins`
3. **选择 `Available Plugins`**
4. **勾选要安装的插件**，点击 `Install`
5. **重启 `VisualVM`**，插件生效

---

### **5.2 常用插件**


| 插件名称 | 分类 | 描述 |
|-----------|-----------|----------------------------------------|
| **GraalJS** | Libraries | GraalVM 的 JavaScript 运行时支持 |
| **VisualVM-Extensions** | Platform | 扩展 `VisualVM` 的基础功能 |
| **BTrace Workbench** | Profiling | BTrace 动态插桩调试 |
| **Startup Profiler** | Profiling | 监测 JVM 启动性能 |
| **VisualVM-Security** | Security | 提供 `VisualVM` 的安全增强 |
| **VisualVM-JConsole** | Tools | `JConsole` 集成到 `VisualVM` |
| **Threads Inspector** | Tools | 线程分析工具 |
| **VisualVM-JFR Streaming** | Tools | Java Flight Recorder（JFR）支持 |
| **VisualVM-BufferMonitor** | Tools | 监测 JVM 缓冲区使用情况 |
| **VisualVM-TDA-Module** | Tools | 线程 Dump 分析模块 |
| **KillApplication** | Tools | 允许直接终止 Java 进程 |
| **VisualVM-Coherence** | Tools | Oracle Coherence 监控 |
| **Visual GC** | Tools | 可视化 GC 监控 |
| **VisualVM-MBeans** | Tools | MBeans 监控 |
| **Tracer-Monitor Probes** | Tracer | 监测 JVM 事件 |
| **Tracer-Collections Probes** | Tracer | 跟踪 Java 集合框架 |
| **Tracer-JavaFX Probes** | Tracer | 监测 JavaFX 相关操作 |
| **Tracer-IO Probes** | Tracer | 监测 IO 操作（文件、网络）|
| **Tracer-Swing Probes** | Tracer | 监测 Swing UI 事件 |
| **Tracer-Jvmstat Probes** | Tracer | 监测 JVMStat 数据 |
| **Tracer-JVM Probes** | Tracer | JVM 内部事件监测 |
| **OQL Syntax Support** | UI | 支持 OQL 查询 Heap Dump |


