# 工具-jps

`jps`（Java Virtual Machine Process Status Tool）是 JDK 提供的一个用于列出当前系统上运行的 Java 进程的工具。它类似于 Linux 的 `ps` 命令，但专门用于 Java 进程。

---

## **1. `jps` 的基本用法**
`jps` 命令的基本格式：
```sh

jps [options] [hostid]
```
如果不带参数，默认列出当前用户拥有的 Java 进程的 `PID` 和 `主类名`。

### **示例**
```sh

jps
```
输出示例：
```
12345 MyApplication
67890 Jps
```
- `12345` 是 `MyApplication` 进程的 PID。
- `67890` 是 `jps` 进程自身。

---

## **2.选项**
| 选项  | 作用 |
|-------|------|
| `-q` | 仅显示 Java 进程 ID，不显示类名 |
| `-m` | 显示主类的参数 |
| `-l` | 显示主类的全限定名或 JAR 包路径 |
| `-v` | 显示 JVM 参数 |
| `-J<option>` | 传递 JVM 选项给 `jps` 进程 |





## **3.实现原理**
`jps` 通过 JVM 内置的 Attach API 进行进程发现：
1. `jps` 首先扫描 `/tmp/hsperfdata_<user>/` 目录，查找所有 Java 进程的 `PID`。
2. 通过 Attach API 连接到目标 Java 进程，获取详细信息（如类名、JVM 参数等）。
3. 输出进程信息。

