# 工具-jhsdb

`jhsdb`（Java HotSpot Debugger）是 **进程外调试工具**，用于分析运行中的 Java 进程（JVM），或者分析 core dump 文件。  

## **1.命令格式**

```sh

jhsdb <subcommand> <options>
```
其中 `<subcommand>` 是子命令，例如 `jstack`, `jmap`, `jsnap` 等。

常见的选项：
- `--pid <PID>` ：指定要分析的 Java 进程 ID（通过 `jps` 获取）。
- `--exe <path>` ：指定 core dump 关联的 JVM 可执行文件路径。
- `--core <core-file>` ：分析 core dump 文件。

---

## **2.子命令**

### **2.1 `jhsdb jstack`（线程堆栈分析）**

相当于 `jstack` 命令，获取进程的线程栈信息：
```sh

jhsdb jstack --pid <PID>
```
示例：
```sh

jhsdb jstack --pid 12345
```


### **2.2 `jhsdb jmap`（堆内存分析）**
类似于 `jmap`，可以输出 JVM 进程的内存使用情况：
```sh

jhsdb jmap --pid <PID>
```
示例：
```sh

jhsdb jmap --pid 12345
```


### **2.3 `jhsdb jsnap`（快照）**
用于创建 JVM 运行状态快照，方便事后分析：
```sh

jhsdb jsnap --pid <PID>
```



### **2.4 `jhsdb jinfo`（JVM 配置信息）**
类似于 `jinfo`，用于获取 JVM 运行参数：
```sh

jhsdb jinfo --pid <PID>
```


### **2.5 `jhsdb debugd`（远程调试服务器）**
启动远程调试服务器，可以让其他 `jhsdb` 进程连接：
```sh

jhsdb debugd --pid <PID>
```
然后，使用 `jhsdb` 远程连接：
```sh

jhsdb attach --connect localhost:<port>
```


