# java基础-Function包

### **`java.util.function` 包介绍**
`java.util.function` 是 **Java 8** 引入的 **函数式接口**库，主要用于 **Lambda 表达式** 和 **函数式编程**。它提供了一组通用的 **函数接口**，用于 **处理数据转换、条件判断、消费数据、提供数据** 等操作。

---

## **1. `java.util.function` 包的核心接口**
### **（1）`Function<T, R>`：转换函数**
- **作用**：接受一个参数 `T`，返回一个 `R`。
- **常见用途**：
  - **数据转换**（例如，把字符串转换成整数）。
  - **流（Stream）中的 `map()` 操作**。

```java
import java.util.function.Function;

public class FunctionExample {
    public static void main(String[] args) {
        Function<String, Integer> strToLength = str -> str.length();
        System.out.println(strToLength.apply("hello")); // 输出: 5
    }
}
```

---

### **（2）`Consumer<T>`：消费型函数**
- **作用**：接受一个参数 `T`，不返回结果（**执行某种操作**）。
- **常见用途**：
  - **打印日志**
  - **遍历集合**

```java
import java.util.function.Consumer;

public class ConsumerExample {
    public static void main(String[] args) {
        Consumer<String> printUpperCase = str -> System.out.println(str.toUpperCase());
        printUpperCase.accept("hello"); // 输出: HELLO
    }
}
```

---

### **（3）`Supplier<T>`：提供者**
- **作用**：不接受参数，返回一个值 `T`（**通常用于懒加载**）。
- **常见用途**：
  - **提供默认值**
  - **生成随机数**

```java
import java.util.function.Supplier;
import java.util.Random;

public class SupplierExample {
    public static void main(String[] args) {
        Supplier<Integer> randomSupplier = () -> new Random().nextInt(100);
        System.out.println(randomSupplier.get()); // 输出: 随机数
    }
}
```

---

### **（4）`Predicate<T>`：条件判断**
- **作用**：接受 `T` 类型参数，返回 `boolean`（**用于条件判断**）。
- **常见用途**：
  - **过滤集合**
  - **校验输入数据**

```java
import java.util.function.Predicate;

public class PredicateExample {
    public static void main(String[] args) {
        Predicate<Integer> isEven = num -> num % 2 == 0;
        System.out.println(isEven.test(10)); // 输出: true
        System.out.println(isEven.test(11)); // 输出: false
    }
}
```

---

## **2. 其他变种接口**
### **（1）BiFunction<T, U, R>`：双参数转换**
- **作用**：接受 **两个参数** `T` 和 `U`，返回 `R`。
- **常见用途**：
  - **两个数相加**
  - **合并数据**

```java
import java.util.function.BiFunction;

public class BiFunctionExample {
    public static void main(String[] args) {
        BiFunction<Integer, Integer, Integer> sum = (a, b) -> a + b;
        System.out.println(sum.apply(3, 5)); // 输出: 8
    }
}
```

---

### **（2）BiConsumer<T, U>`：双参数消费者**
- **作用**：接受 **两个参数**，不返回值。
- **常见用途**：
  - **遍历 `Map<K, V>`**
  - **日志打印**

```java
import java.util.function.BiConsumer;
import java.util.Map;
import java.util.HashMap;

public class BiConsumerExample {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("apple", 10);
        map.put("banana", 20);

        BiConsumer<String, Integer> printEntry = (key, value) -> System.out.println(key + ": " + value);
        map.forEach(printEntry);
    }
}
```

---

### **（3）UnaryOperator<T>`：一元操作**
- **作用**：**输入和输出类型相同**，适用于 **对单个元素的转换**。
- **常见用途**：
  - **字符串变换**
  - **数学运算**

```java
import java.util.function.UnaryOperator;

public class UnaryOperatorExample {
    public static void main(String[] args) {
        UnaryOperator<Integer> square = x -> x * x;
        System.out.println(square.apply(5)); // 输出: 25
    }
}
```

---

### **（4）BinaryOperator<T>`：双参数运算**
- **作用**：**输入和输出类型相同**，适用于 **累加、最大值最小值计算**。
- **常见用途**：
  - **计算最大值**
  - **累加操作**

```java
import java.util.function.BinaryOperator;

public class BinaryOperatorExample {
    public static void main(String[] args) {
        BinaryOperator<Integer> maxOperator = (a, b) -> Math.max(a, b);
        System.out.println(maxOperator.apply(10, 20)); // 输出: 20
    }
}
```

---

## **3. `java.util.function` 在 `Stream` API 中的应用**
`java.util.function` 的接口广泛用于 **Stream API**：
- **`map(Function<T, R>)`** → 数据转换
- **`filter(Predicate<T>)`** → 数据过滤
- **`forEach(Consumer<T>)`** → 遍历数据
- **`reduce(BinaryOperator<T>)`** → 归约计算

```java
import java.util.List;
import java.util.Arrays;
import java.util.function.Function;
import java.util.function.Predicate;
import java.util.function.Consumer;

public class StreamExample {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("apple", "banana", "cherry", "date");

        // 过滤长度 > 5 的单词，然后转换成大写，再打印
        words.stream()
             .filter(word -> word.length() > 5)  // Predicate
             .map(String::toUpperCase)           // Function
             .forEach(System.out::println);      // Consumer
    }
}
```

---

## **4. 总结**
| **接口** | **参数** | **返回值** | **用途** |
|----------|---------|---------|---------|
| `Function<T, R>` | `T` | `R` | 数据转换 |
| `Consumer<T>` | `T` | `void` | 执行操作（日志、打印等） |
| `Supplier<T>` | 无 | `T` | 提供数据（懒加载） |
| `Predicate<T>` | `T` | `boolean` | 条件判断（过滤数据） |
| `BiFunction<T, U, R>` | `T, U` | `R` | 两个参数转换 |
| `BiConsumer<T, U>` | `T, U` | `void` | 处理两个参数（`Map` 遍历） |
| `UnaryOperator<T>` | `T` | `T` | 一元运算（平方、大小写转换） |
| `BinaryOperator<T>` | `T, T` | `T` | 二元运算（累加、最大值） |
