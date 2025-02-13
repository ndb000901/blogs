# java基础-包

## **1. 什么是 Java 包？**
Java **包（Package）** 是一种用于组织类、接口和子包的机制，可以避免命名冲突并提供访问控制。类似于文件系统中的目录，包可以帮助管理 Java 代码结构，使代码更易维护和复用。

---

## **2. 如何声明包？**
在 Java 源文件的第一行使用 `package` 关键字声明包名。

### **示例**
```java
package com.example.utils;

public class MathUtils {
    public static int add(int a, int b) {
        return a + b;
    }
}
```
**说明：**  
- `package com.example.utils;` 定义了该类 `MathUtils` 属于 `com.example.utils` 包。
- 包名通常采用 **公司域名倒序+项目名+模块名** 的格式，如 `com.company.project.module`。

---

## **3. 如何使用 `import` 关键字导入包中的类？**
在使用其他包的类时，需要使用 `import` 关键字。

### **示例**
```java
import com.example.utils.MathUtils;  // 导入 MathUtils 类

public class Main {
    public static void main(String[] args) {
        int sum = MathUtils.add(5, 3);
        System.out.println("Sum: " + sum);
    }
}
```
**说明：**  
- `import com.example.utils.MathUtils;` 允许 `Main` 类使用 `MathUtils` 里的方法。
- `import com.example.utils.*;` 可以导入整个 `com.example.utils` 包下的所有类（不推荐，可能会引入不必要的类）。

---

## **4. 默认包（Unnamed Package）**
如果 Java 文件没有声明 `package`，它会自动属于 **默认包**，但默认包中的类无法被其他包访问。  

**示例（默认包）**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```
**注意**：不建议使用默认包，尤其是在大型项目中，因为它会影响代码的组织结构和可维护性。

---

## **5. 包的访问修饰符**
Java 提供四种访问级别，控制不同包之间的访问权限：

| 访问修饰符 | 同一类 | 同一包 | 子类（不同包） | 其他包 |
|-----------|--------|--------|-------------|--------|
| `public` | ✅ | ✅ | ✅ | ✅ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| 无修饰符（默认） | ✅ | ✅ | ❌ | ❌ |
| `private` | ✅ | ❌ | ❌ | ❌ |

### **示例**
```java
package com.example;

public class Person {
    public String name = "John";   // 任何地方可访问
    protected int age = 30;        // 同包和子类可访问
    String address = "USA";        // 仅限于同包
    private String password = "1234"; // 仅限于当前类
}
```

---



