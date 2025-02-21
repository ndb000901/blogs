# java基础-注释

# **Java 注释详解**  

Java 提供了三种类型的注释：  
- **单行注释 (`//`)**
- **多行注释 (`/* ... */`)**
- **文档注释 (`/** ... */`)**

## **1. 单行注释 (`//`)**
单行注释用于对代码进行简单的说明，只占据当前行。  

### **示例**
```java
// 这是一个单行注释
int x = 10; // 变量 x 赋值为 10
```

---

## **2. 多行注释 (`/* ... */`)**
多行注释用于较长的注释，可以跨多行。  

### **示例**
```java
/*
   这是一个多行注释，
   可以跨越多行
*/
int y = 20;
```

---

## **3. 文档注释 (`/** ... */`)**
文档注释 (`Javadoc`) 用于生成 API 文档，通常用于类、方法和变量的说明。  

### **示例**
```java
/**
 * 计算两个数的和
 * @param a 第一个整数
 * @param b 第二个整数
 * @return 两数之和
 */
public int add(int a, int b) {
    return a + b;
}
```

---

## **4. Javadoc 常见标签**
文档注释中可以使用 Javadoc 标签来提供更详细的信息。  

| 标签        | 说明 |
|------------|----------------|
| `@author`  | 指定类或方法的作者 |
| `@version` | 指定版本号 |
| `@param`   | 说明方法的参数 |
| `@return`  | 说明方法的返回值 |
| `@throws`  | 说明可能抛出的异常 |
| `@see`     | 参考某个类或方法 |
| `@deprecated` | 标注方法已过时 |
| `@since`   | 指定方法或类从哪个版本开始可用 |

### **示例**
```java
/**
 * 计算两个整数的乘积
 * @author John Doe
 * @version 1.0
 * @param a 乘数
 * @param b 被乘数
 * @return 乘积
 * @throws IllegalArgumentException 如果 a 或 b 为负数
 */
public int multiply(int a, int b) {
    if (a < 0 || b < 0) {
        throw new IllegalArgumentException("参数不能为负数");
    }
    return a * b;
}
```

---

## **5. 使用 `javadoc` 生成 HTML 文档**
在 Java 项目中，可以使用 `javadoc` 工具生成 API 文档。例如，在命令行运行：
```sh
javadoc -d docs MyClass.java
```
它会在 `docs/` 目录下生成 HTML 格式的 API 文档。

---

## **6. 注释的最佳实践**
- **单行注释**：用于简短的代码解释。
- **多行注释**：用于模块级的说明，避免冗余。
- **文档注释**：用于公共 API，方便维护和阅读。
- **避免过多注释**：代码本身应该足够清晰，避免注释冗余或误导。

### **示例：良好的注释**
```java
/**
 * 表示一个用户
 */
public class User {
    private String name; // 用户姓名

    /**
     * 获取用户姓名
     * @return 用户的姓名
     */
    public String getName() {
        return name;
    }
}
```
