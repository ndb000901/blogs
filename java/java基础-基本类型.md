# java基础-基本类型

## 1.基本类型

| 数据类型   | 大小         | 默认值    | 取值范围                            |
|------------|--------------|-----------|---------------------------------|
| **byte**   | 8位          | 0         | -128 到 127                      |
| **short**  | 16位         | 0         | -32,768 到 32,767                |
| **int**    | 32位         | 0         | -2^31 到 2^31 - 1                |
| **long**   | 64位         | 0L        | -2^63 到 2^63 - 1                |
| **float**  | 32位         | 0.0f      | ±1.4E-45 到 ±3.4E38 (约7位有效数字)    |
| **double** | 64位         | 0.0d      | ±4.9E-324 到 ±1.8E308 (约15位有效数字) |
| **char**   | 16位         | '\u0000'  | 0 到 65535 (Unicode字符) UTF-16 编码 |
| **boolean**| -            | false     | `true` 或 `false`                |

## 2.包装类

Java 提供了 **基本数据类型** 的 **包装类**，这些包装类位于 `java.lang` 包下。包装类是对基本数据类型的封装，提供了更多的方法和功能，如对象的比较、转换等。

以下是 Java 基本类型及其对应的包装类：

| 基本数据类型 | 包装类        | 特性                                          |
|--------------|---------------|---------------------------------------------|
| `byte`       | `Byte`        | `Byte` 类是 `byte` 的包装类。                |
| `short`      | `Short`       | `Short` 类是 `short` 的包装类。              |
| `int`        | `Integer`     | `Integer` 类是 `int` 的包装类。              |
| `long`       | `Long`        | `Long` 类是 `long` 的包装类。                |
| `float`      | `Float`       | `Float` 类是 `float` 的包装类。              |
| `double`     | `Double`      | `Double` 类是 `double` 的包装类。            |
| `char`       | `Character`   | `Character` 类是 `char` 的包装类。          |
| `boolean`    | `Boolean`     | `Boolean` 类是 `boolean` 的包装类。          |

### 包装类的功能：
1. **类型转换**：包装类提供了将基本数据类型与字符串之间的转换。
   - `parseInt(String)`、`parseDouble(String)`等方法将字符串解析为基本数据类型。
   - `valueOf()`方法将基本类型转换为对应的包装类。
   
   例如：
   ```java
   int num = Integer.parseInt("123");  // 字符串转换为基本数据类型
   String str = Integer.toString(123); // 基本数据类型转换为字符串
   ```

2. **常量值**：每个包装类都提供了该类型的最大值和最小值。例如：
   - `Integer.MAX_VALUE`（`int`类型的最大值）
   - `Double.MIN_VALUE`（`double`类型的最小值）

3. **对象方法**：包装类是对象，可以调用方法，如 `equals()`、`compareTo()` 等。例如：
   ```java
   Integer a = 10;
   Integer b = 10;
   System.out.println(a.equals(b)); // 输出 true
   ```

4. **自动装箱与拆箱**（Autoboxing and Unboxing）：
   - **自动装箱**：基本数据类型自动转换为对应的包装类对象。
   - **自动拆箱**：包装类对象自动转换为基本数据类型。

   例如：
   ```java
   Integer x = 10; // 自动装箱，将 int 转为 Integer
   int y = x;      // 自动拆箱，将 Integer 转为 int
   ```

### 示例代码：
```java
public class WrapperExample {
    public static void main(String[] args) {
        // 基本数据类型到包装类的转换
        int i = 10;
        Integer integerObj = Integer.valueOf(i);  // 装箱
        System.out.println("Integer Object: " + integerObj);

        // 包装类到基本数据类型的转换
        Integer anotherInt = 20;
        int j = anotherInt.intValue();  // 拆箱
        System.out.println("Primitive int: " + j);

        // 自动装箱和拆箱
        Integer autoBoxed = 30;  // 自动装箱
        int autoUnboxed = autoBoxed;  // 自动拆箱
        System.out.println("AutoBoxed: " + autoBoxed);
        System.out.println("AutoUnboxed: " + autoUnboxed);

        // 使用包装类的常量
        System.out.println("Max value of Integer: " + Integer.MAX_VALUE);
        System.out.println("Min value of Integer: " + Integer.MIN_VALUE);
    }
}
```
