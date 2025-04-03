# 工具-jconsole

`jconsole`（Java Monitoring and Management Console）是 **JDK 自带的可视化监控工具**，用于监视 **JVM 内存、线程、类加载、GC 及 MBeans**，适合对 **Java 应用进行实时性能分析和故障排查**。  

---

## **1. 启动 `jconsole`**
### **方式 1：直接启动**
```sh

jconsole
```
会弹出 GUI 界面，显示本地可用的 Java 进程。

### **方式 2：指定远程 JVM 进程**
```sh

jconsole <host>:<port>
```
示例：
```sh

jconsole 192.168.1.100:9999
```
**注意**：远程连接需要 **开启 JMX 远程管理**（见**远程监控**部分）。

---

## **2. 连接本地或远程 JVM**

启动 `jconsole` 后，可以看到 **Java 进程列表**，选择要监控的进程：
1. **本地进程**：直接选择进程 ID（PID）。
2. **远程进程**：
   - 需要在 Java 应用启动时启用 JMX：
     ```sh
     java -Dcom.sun.management.jmxremote \
          -Dcom.sun.management.jmxremote.port=9999 \
          -Dcom.sun.management.jmxremote.authenticate=false \
          -Dcom.sun.management.jmxremote.ssl=false \
          -jar yourApp.jar
     ```
   - 在 `jconsole` 中输入 **IP:端口** 连接远程 JVM。

---

## **3. 主要监控项**

### **3.1 概览（Overview）**
展示 JVM 运行的 **CPU 使用率、内存使用情况、线程数量、类加载数量** 等。

### **3.2 内存（Memory）**
监控 JVM 内存的实时使用情况：
- **Heap（堆内存）**：
  - **Eden Space**：新生代 Eden 区
  - **Survivor Space**：新生代 Survivor 区
  - **Old Gen**：老年代
- **Non-Heap（非堆内存）**：
  - **Metaspace**：类元数据存储空间
  - **Code Cache**：JIT 编译缓存
  - **Compressed Class Space**：类元数据压缩空间

**功能**：
- **查看 GC 频率**（是否频繁触发 Full GC）。
- **发现内存泄漏**（如果 Metaspace 持续增长，可能是类加载泄漏）。
- **手动触发 GC**（点击 `Perform GC`）。

---

### **3.3 线程（Threads）**
监视 JVM 线程运行情况：
- **查看线程状态**（RUNNABLE、WAITING、BLOCKED）
- **检测死锁**（Deadlock detection）
- **分析线程 CPU 占用**（排查性能问题）

**功能**：
- 发现 **死锁** 和 **长时间阻塞的线程**。
- **查看 `Thread Dump`**（类似 `jstack`）。

---

### **3.4. 类（Classes）**
监控类加载情况：
- **Loaded classes**：已加载的类数量。
- **Unloaded classes**：卸载的类数量。
- **Total classes**：总共加载的类数量。

**用途**：
- **发现 `ClassLoader` 内存泄漏**（如果 `Loaded Classes` 持续增长）。
- **监控动态类加载**（例如反射、ASM 动态代理）。

---

### **3.5. VM 概要（VM Summary）**
显示 JVM 版本、启动参数、系统属性等。

**用途**：
- 检查 **JVM 启动参数** 是否符合预期（如 `-Xmx`、GC 策略）。
- 确认 **GC 日志** 是否开启。

---

### **3.6. MBeans（管理 Java 进程）**
JConsole 提供 **MBean 监控功能**，可以查看和修改 JVM 的 **动态参数**：
- 监控 **GC 信息**
- 监控 **线程池大小**
- 调整 **日志级别**
- 获取 **应用自定义指标**

**用途**：
- **动态调整 JVM 运行时参数**（例如修改线程池大小）。
- **实时查看应用程序的自定义指标**（如业务统计数据）。

---

## **4. 远程监控 `jconsole`**
如果想 **远程监控 Java 进程**，需要开启 JMX 远程管理：

### **4.1 在远程服务器上启用 JMX**
启动 Java 应用时添加以下参数：
```sh

-Dcom.sun.management.jmxremote \
-Dcom.sun.management.jmxremote.port=9999 \
-Dcom.sun.management.jmxremote.rmi.port=9998 \
-Dcom.sun.management.jmxremote.local.only=false \
-Dcom.sun.management.jmxremote.authenticate=false \
-Dcom.sun.management.jmxremote.ssl=false \
-Djava.rmi.server.hostname=192.168.1.100
```
- `9999`：JMX 端口（用于 `jconsole` 连接）。
- `9998`：RMI 端口（解决防火墙问题）。
- `-Djava.rmi.server.hostname`：**必须设置成服务器 IP，否则 `jconsole` 连接会失败！**

