# Properties

`Properties` 是 Java 提供的一个专用于读取和写入**配置文件**（特别是 `.properties` 文件）的类，本质上是一个 **键值均为字符串（String）的 `Hashtable` 子类**。

它常用于读取应用配置，如数据库连接参数、国际化文本等。

---

## 1. 类定义

```java
public class Properties extends Hashtable<Object, Object>
```

* 所属包：`java.util`
* 父类：`Hashtable<Object, Object>`
* 特殊性：**只允许 String 类型的 key 和 value**，但由于继承自 Hashtable，其泛型是 `Object,Object`，编译时不能强制限制。

---

## 2. 使用场景

* 加载 `.properties` 文件配置（Java 配置约定）
* 持久化程序配置
* 系统属性操作（System.getProperties）
* 国际化支持（结合 ResourceBundle）

---

## 3. 常用方法

| 方法                                            | 说明                        |
| --------------------------------------------- | ------------------------- |
| `load(InputStream inStream)`                  | 从输入流读取 `.properties` 文件内容 |
| `load(Reader reader)`                         | 读取字符流（推荐 UTF-8 文件用此）      |
| `getProperty(String key)`                     | 获取指定 key 的 value（String）  |
| `setProperty(String key, String value)`       | 设置属性值                     |
| `store(OutputStream out, String comments)`    | 将属性保存到输出流（写入文件）           |
| `store(Writer writer, String comments)`       | 字符流写出                     |
| `list(PrintStream out)`                       | 打印所有属性键值对到指定输出流           |
| `stringPropertyNames()`                       | 获取所有 key（返回 Set<String>）  |
| `loadFromXML(InputStream in)`                 | 从 XML 配置读取属性              |
| `storeToXML(OutputStream os, String comment)` | 将属性写入 XML                 |

---

## 4. 使用示例

### 4.1 加载 .properties 文件

假设 `config.properties` 内容如下：

```properties
username=admin
password=123456
timeout=5000
```

Java 代码读取：

```java
Properties props = new Properties();
try (InputStream input = new FileInputStream("config.properties")) {
    props.load(input);

    String user = props.getProperty("username");
    String pwd = props.getProperty("password");
    System.out.println(user + " / " + pwd);
}
```

---

### 4.2 写入 properties 文件

```java
Properties props = new Properties();
props.setProperty("name", "ChatGPT");
props.setProperty("version", "4.0");

try (OutputStream output = new FileOutputStream("output.properties")) {
    props.store(output, "App Info");
}
```

生成文件内容如下：

```properties
#App Info
#2025-05-07
name=ChatGPT
version=4.0
```

---

### 4.3 结合系统属性使用

```java
Properties sysProps = System.getProperties();
String os = sysProps.getProperty("os.name");
System.out.println("操作系统: " + os);
```

---

## 5. 注意事项

* 只能存储字符串键值对（虽然底层是 Hashtable\<Object,Object>）
* `.properties` 文件默认使用 ISO-8859-1 编码，非英文字符需 Unicode 转码（或使用 `Reader` 支持 UTF-8）
* 同步线程安全（继承自 Hashtable），但性能低于现代并发集合

---



