# NIO 操作文件系统

## 1. 核心类

| 类 | 说明 |
|----|------|
| `Paths` | 用于创建 `Path` 实例（静态工厂类） |
| `Path` | 表示文件或目录路径 |
| `Files` | 工具类，操作文件内容（读写、复制、移动等） |
| `FileSystem` | 表示整个文件系统（可跨平台） |
| `FileStore` | 表示磁盘分区或存储设备 |
| `WatchService` | 文件系统事件监听（新增、删除、修改） |

---

## 2. 示例

### 2.1 创建 Path 对象

```java
Path path = Paths.get("data/test.txt");
```

可自动解析相对/绝对路径，支持 `/`、`..`、符号链接等。

---

### 2.2 文件和目录操作

#### 创建文件或目录

```java
Files.createFile(Paths.get("data/newFile.txt"));
Files.createDirectories(Paths.get("data/nested/dir"));
```

#### 删除文件或目录

```java
Files.deleteIfExists(Paths.get("data/old.txt"));
```

#### 移动或重命名文件

```java
Files.move(Paths.get("data/a.txt"), Paths.get("data/b.txt"), StandardCopyOption.REPLACE_EXISTING);
```

#### 复制文件

```java
Files.copy(Paths.get("data/a.txt"), Paths.get("data/copy.txt"), StandardCopyOption.REPLACE_EXISTING);
```

---

### 2.3 读写文件内容

#### 读取所有行

```java
List<String> lines = Files.readAllLines(Paths.get("data/test.txt"), StandardCharsets.UTF_8);
lines.forEach(System.out::println);
```

#### 写入文件（覆盖/追加）

```java
Files.write(Paths.get("data/out.txt"), 
            Arrays.asList("hello", "world"), 
            StandardCharsets.UTF_8,
            StandardOpenOption.CREATE, 
            StandardOpenOption.APPEND);
```

#### 流式读写（大文件推荐）

```java
try (BufferedReader reader = Files.newBufferedReader(path)) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}
```

---

### 2.4 遍历文件目录

#### 遍历目录下所有文件

```java
Files.walk(Paths.get("data"))
     .filter(Files::isRegularFile)
     .forEach(System.out::println);
```

#### 查找符合条件的文件

```java
try (Stream<Path> stream = Files.find(
         Paths.get("data"), 10,
         (p, attr) -> p.toString().endsWith(".txt"))) {
    stream.forEach(System.out::println);
}
```

---

### 2.5 监听文件系统事件

```java
WatchService watchService = FileSystems.getDefault().newWatchService();
Path dir = Paths.get("data");
dir.register(watchService, StandardWatchEventKinds.ENTRY_CREATE,
                            StandardWatchEventKinds.ENTRY_DELETE,
                            StandardWatchEventKinds.ENTRY_MODIFY);

WatchKey key;
while ((key = watchService.take()) != null) {
    for (WatchEvent<?> event : key.pollEvents()) {
        System.out.println("事件类型: " + event.kind() + "，文件: " + event.context());
    }
    key.reset();
}
```

---

### 2.6 读取文件属性

```java
Path path = Paths.get("data/test.txt");
BasicFileAttributes attrs = Files.readAttributes(path, BasicFileAttributes.class);

System.out.println("创建时间: " + attrs.creationTime());
System.out.println("是否是目录: " + attrs.isDirectory());
System.out.println("文件大小: " + attrs.size());
```

---

