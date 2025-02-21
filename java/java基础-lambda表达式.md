# java基础-lambda表达式

**Lambda 表达式** 是 Java 8 引入的一项新特性，它允许以更加简洁和清晰的方式编写匿名方法（即不需要显式地声明方法）。Lambda 表达式主要用于实现**函数式接口**，使得代码更加简洁和灵活，特别适用于对集合的操作、事件监听、回调等场景。

## **1. Lambda 表达式的基本语法**
Lambda 表达式的基本语法如下：

```java
(parameters) -> expression
```

或

```java
(parameters) -> { statements; }
```

- **parameters**：参数列表，可以是一个或多个，也可以为空。
- **->**：Lambda 操作符，用于分隔参数和表达式（或语句块）。
- **expression**：一个表达式，或者是一个方法体（可以是多条语句）。

### **示例：Lambda 表达式的简单例子**
```java
// 不带参数的 Lambda 表达式
Runnable r = () -> System.out.println("Hello, Lambda!");
r.run(); // 输出：Hello, Lambda!

// 带参数的 Lambda 表达式
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
System.out.println(add.apply(3, 5)); // 输出：8
```

---

## **2. Lambda 表达式的组成部分**
Lambda 表达式通常由以下部分组成：

- **参数列表**：Lambda 表达式的输入参数，可以是一个或多个，类型可以推断。
- **箭头操作符 `->`**：将参数列表和 Lambda 表达式的主体分开。
- **主体**：Lambda 表达式的实现，可能是一个表达式，也可以是多条语句。

### **示例：多条语句的 Lambda**
```java
// Lambda 表达式主体包含多条语句
BinaryOperator<Integer> multiply = (a, b) -> {
    System.out.println("Multiplying " + a + " and " + b);
    return a * b;
};
System.out.println(multiply.apply(4, 5));  // 输出：Multiplying 4 and 5 \n 20
```

---

## **3. 使用 Lambda 表达式**
Lambda 表达式广泛应用于 Java 的一些常用接口，特别是**函数式接口**。函数式接口是只包含一个抽象方法的接口，它可以包含多个默认方法或静态方法。

### **3.1 函数式接口示例**
Java 8 提供了许多常见的函数式接口，比如 `Runnable`、`Comparator`、`Function`、`Predicate` 等。

#### **示例：使用 Lambda 表达式实现 Runnable 接口**
```java
Runnable r = () -> System.out.println("Hello from Lambda!");
Thread t = new Thread(r);
t.start();
```

#### **示例：使用 Lambda 表达式实现 `Comparator` 接口**
```java
import java.util.*;

class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return name + " (" + age + ")";
    }
}

public class LambdaDemo {
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(new Person("Alice", 25), new Person("Bob", 30), new Person("Charlie", 20));

        // 使用 Lambda 表达式对 List 进行排序
        people.sort((p1, p2) -> Integer.compare(p1.age, p2.age));

        for (Person p : people) {
            System.out.println(p);
        }
    }
}
```
**输出：**
```
Charlie (20)
Alice (25)
Bob (30)
```

---

## **4. Lambda 表达式常见用法**
### **4.1 使用 `Predicate` 进行条件测试**
`Predicate` 是一个函数式接口，接受一个参数并返回一个布尔值。

```java
import java.util.function.Predicate;

Predicate<Integer> isEven = (x) -> x % 2 == 0;
System.out.println(isEven.test(4)); // 输出：true
System.out.println(isEven.test(5)); // 输出：false
```

### **4.2 使用 `Function` 进行转换**
`Function` 接口接受一个输入并返回一个输出，通常用于数据转换。

```java
import java.util.function.Function;

Function<String, Integer> stringLength = (str) -> str.length();
System.out.println(stringLength.apply("Hello")); // 输出：5
```

### **4.3 使用 `Consumer` 对对象进行操作**
`Consumer` 接口接受一个输入并执行某种操作，不返回结果。

```java
import java.util.function.Consumer;

Consumer<String> printUpperCase = (str) -> System.out.println(str.toUpperCase());
printUpperCase.accept("hello");  // 输出：HELLO
```

---

## **5. Lambda 表达式与方法引用**
Java 8 引入了**方法引用**，它可以用更简洁的方式来表示 Lambda 表达式。方法引用通过 `::` 符号引用方法。

### **示例：方法引用**
```java
import java.util.List;
import java.util.Arrays;

// 使用 Lambda 表达式
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
names.forEach(name -> System.out.println(name));

// 使用方法引用
names.forEach(System.out::println); // 等同于上面的 Lambda 表达式
```

---

## **6. Lambda 表达式的类型推断**
Java 编译器能够根据上下文自动推断 Lambda 表达式的参数类型，因此，参数类型可以省略。

### **示例：类型推断**
```java
// 使用 Lambda 表达式时可以省略参数类型
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
numbers.forEach(num -> System.out.println(num * 2));
```

编译器可以根据 `forEach` 方法的参数类型推断出 `num` 的类型是 `Integer`，因此可以省略类型声明。

---

## **7. Lambda 表达式的优势**
- **简洁**：Lambda 表达式简化了代码，减少了匿名类的冗余。
- **可读性高**：用更简洁的方式表达复杂的逻辑。
- **提高效率**：特别适合用于并行处理和流式处理（Streams）。

---

## **8. 使用 Lambda 表达式的场景**
Lambda 表达式通常用于以下场景：
- **集合操作**：通过 `forEach`、`map`、`filter` 等操作集合数据。
- **事件监听**：用于回调函数、事件处理等。
- **并行处理**：结合 `Streams` API 对数据进行并行处理。

### **示例：使用 Lambda 表达式与 Stream API**
```java
import java.util.*;
import java.util.stream.*;

public class LambdaStreamDemo {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("apple", "banana", "cherry", "date");

        // 使用 Stream 和 Lambda 表达式过滤并转换数据
        List<String> result = words.stream()
                                    .filter(w -> w.length() > 5)
                                    .map(String::toUpperCase)
                                    .collect(Collectors.toList());

        result.forEach(System.out::println);  // 输出：BANANA, CHERRY
    }
}
```

---

## **9. Lambda 表达式与并发**
Lambda 表达式和 `Stream` API 常常结合使用，用于并行处理数据。

### **示例：并行流处理**
```java
import java.util.*;
import java.util.stream.*;

public class ParallelStreamDemo {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        // 使用并行流和 Lambda 表达式进行并行处理
        int sum = numbers.parallelStream()
                          .mapToInt(Integer::intValue)
                          .sum();

        System.out.println("Sum: " + sum);  // 输出：Sum: 15
    }
}
```

---

