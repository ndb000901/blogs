# 工具-jinfo

`jinfo` 是 JDK 提供的一个诊断工具，用于**查询和修改 Java 进程的配置信息**，包括 JVM 运行时参数、系统属性等。它可以帮助排查 JVM 相关问题，特别是在**动态调整 JVM 参数**时非常有用。

---

## **1. `jinfo` 基本语法**
```bash

jinfo [options] <pid>
```
- `<pid>`：Java 进程 ID，可以通过 `jps` 获取。
- `[options]`：不同的 `jinfo` 选项，用于查看或修改 JVM 运行参数。

---

## **2.示例**

### **2.1 查询系统属性**
```bash

jinfo -sysprops <pid>
```
**作用**：列出 Java 进程的所有系统属性（System Properties），等效于 `System.getProperties()`。

**示例**
```bash

jinfo -sysprops 12345
```
**输出示例**
```
java.vendor = Oracle Corporation
java.version = 1.8.0_281
os.name = Linux
os.arch = amd64
os.version = 5.10.0-8-amd64
user.name = root
user.home = /root
user.dir = /usr/local/app
```
**用途**：
- 查看 JVM 运行的环境信息，如 `java.version`、`os.name` 等。
- 检查 `user.dir`（当前工作目录），避免因工作目录问题导致 `ClassNotFoundException`。

---

### **2.2 查询 JVM 运行参数**
```bash

jinfo -flags <pid>
```
**作用**：列出 Java 进程的所有 JVM 运行参数，包括默认参数和用户指定参数。

**示例**
```bash

jinfo -flags 12345
```
**输出示例**
```
-XX:InitialHeapSize=268435456
-XX:MaxHeapSize=4294967296
-XX:+UseG1GC
-XX:+UseCompressedOops
```
**用途**：
- 检查 `-Xmx`、`-Xms` 是否生效。
- 确定垃圾回收器（如 `UseG1GC`）。
- 查看是否开启了 `CompressedOops`（指针压缩）。

---

### **2.3 查看所有 JVM 选项**
```bash

jinfo -flag <flag-name> <pid>
```
**作用**：查看某个特定的 JVM 参数值。

**示例**
```bash

jinfo -flag MaxHeapSize 12345
```
**输出**
```
-XX:MaxHeapSize=4294967296
```
**用途**：
- 确定 `-Xmx` 是否生效。
- 查看 `-XX:MetaspaceSize`、`-XX:MaxMetaspaceSize` 配置。

---

### **2.4 动态修改 JVM 选项（部分参数可修改）**
```bash

jinfo -flag [+|-]<flag-name> <pid>
```
**作用**：动态开启或关闭某些 JVM 选项。

**示例**
```bash

jinfo -flag +PrintGCDetails 12345
```
**用途**：
- 在运行时开启 `PrintGCDetails`，无需重启 JVM。

```bash

jinfo -flag -UseCompressedOops 12345
```
**用途**：
- 关闭 `UseCompressedOops`（指针压缩）。

**注意**：**并非所有参数都支持动态修改**，如 `MaxHeapSize` 无法修改。

---

### **2.5 查看 `-XX:+PrintFlagsFinal` 的 JVM 选项**
```bash

jinfo -flags <pid>
```
如果想获取所有 JVM 选项及其默认值，可以使用：
```bash

java -XX:+PrintFlagsFinal -version
```
或者：
```bash

jcmd <pid> VM.flags
```
**示例**
```bash

java -XX:+PrintFlagsFinal -version | grep MaxHeapSize
```
**用途**：
- 获取 JVM 运行时完整参数列表。
- 检查 GC 参数是否符合预期。
