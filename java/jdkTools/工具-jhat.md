# 工具-jhat

`jhat`（JVM Heap Analysis Tool）是 JDK 提供的 Java 堆分析工具，**用于解析 `jmap` 生成的 `heap dump (.hprof)` 文件**，并提供一个 Web 界面来浏览对象、类及其引用关系。

---

## **1. 用法**
```bash

jhat [options] <heap-dump-file>
```
- `<heap-dump-file>`：`jmap -dump` 生成的 `.hprof` 文件。
- `[options]`：可选参数，如指定端口、开启对象查询等。

---

## **2. 常用选项**
| 选项 | 说明 |
|------|------|
| `-port <port>` | 指定 Web 服务器的端口，默认为 `7000` |
| `-J<flag>` | 传递 JVM 选项，例如 `-J-Xmx2g` |
| `-stack <bool>` | 是否显示对象分配的调用栈，默认 `true` |
| `-refs <bool>` | 是否跟踪对象引用，默认 `true` |
| `-baseline <file>` | 用于比较两个 Heap Dump 之间的差异 |
| `-debug` | 详细日志输出 |

---

## **3. 示例**

### **3.1 分析 Heap Dump**
```bash

jhat /tmp/heap_dump.hprof
```
**默认情况下：**
- `jhat` 会开启一个 Web 服务器，端口 `7000`。
- 在浏览器访问 `http://localhost:7000/` 进行分析。

### **3.2 自定义端口**
```bash

jhat -port 8080 /tmp/heap_dump.hprof
```
**访问**：
```
http://localhost:8080/
```

### **3.3 限制 `jhat` 内存**
如果 `.hprof` 文件过大，需要增加 `jhat` 运行的 JVM 内存：
```bash

jhat -J-Xmx4g /tmp/heap_dump.hprof
```
---

## **4. `jhat` Web 界面**

启动后，访问 `http://localhost:7000/` ，可以看到：
1. **All Classes**（所有类）：查看 JVM 内存中的所有类。
2. **Instance Counts**（实例计数）：查看每个类的实例数量，**用于查找哪些对象占用最多内存**。
3. **Heap Histogram**（堆直方图）：类似于 `jmap -histo`，可以看到对象分布情况。
4. **References**（对象引用）：查看对象的引用关系，找出**哪些对象未被正确回收**。
5. **OQL 查询**（类似 SQL 查询堆内存对象）。

---

## **5. OQL 查询**
`jhat` 提供类似 SQL 的 OQL（Object Query Language）用于查询对象信息。

### **5.1 查找大对象**
```sql
SELECT s FROM java.lang.String s WHERE s.count > 1000
```
**用途**：查找字符串池中占用大内存的对象。

### **5.2 查询特定类的对象**
```sql
SELECT * FROM java.util.HashMap
```
**用途**：查找所有 `HashMap` 实例。

### **5.3 查找某个类的实例数量**
```sql
SELECT COUNT(s) FROM java.lang.String s
```

---
