# java基础-异常处理

Java 的异常处理机制用于捕获和处理程序中的错误，以便程序能够优雅地处理异常情况，而不是崩溃。Java 提供了一套完善的异常处理机制，允许开发者通过 `try-catch` 语句块捕获和处理异常。

### 1. **异常的分类**
Java 中的异常可以分为两大类：

- **受检查异常（Checked Exception）**：这类异常在编译时必须处理（通过 `try-catch` 或 `throws`）。通常是程序运行过程中可以预见并需要处理的错误。例如：`IOException`、`SQLException` 等。
  
- **未受检查异常（Unchecked Exception）**：也称为运行时异常，这类异常不要求在编译时强制处理。通常是程序逻辑错误或不可预见的错误。例如：`NullPointerException`、`ArrayIndexOutOfBoundsException` 等。

### 2. **异常的层次结构**
所有异常类都继承自 `Throwable` 类，`Throwable` 下面有两个重要的子类：
- `Error`：表示严重的错误，通常由 JVM 抛出，比如 `OutOfMemoryError`，`StackOverflowError` 等。通常不应该捕获。
- `Exception`：表示程序中的异常，开发者可以捕获并处理的异常。

`Exception` 又分为：
- **受检查异常（Checked Exception）**：如 `IOException`、`SQLException`。
- **未受检查异常（Unchecked Exception）**：如 `NullPointerException`、`ArrayIndexOutOfBoundsException`。

### 3. **异常处理的语法**
Java 使用 `try-catch` 语句块进行异常处理。`try` 块包含可能会抛出异常的代码，`catch` 块捕获并处理异常。

#### 基本的 `try-catch` 语法：
```java
try {
    // 可能会抛出异常的代码
} catch (ExceptionType e) {
    // 处理异常的代码
}
```

#### 示例：
```java
public class ExceptionExample {
    public static void main(String[] args) {
        try {
            int result = 10 / 0;  // 可能会抛出 ArithmeticException
        } catch (ArithmeticException e) {
            System.out.println("An error occurred: " + e.getMessage());
        }
    }
}
```

### 4. **多重 `catch` 块**
Java 允许在同一个 `try` 块后面跟多个 `catch` 块，以捕获不同类型的异常。

#### 示例：
```java
public class MultipleCatchExample {
    public static void main(String[] args) {
        try {
            String str = null;
            int length = str.length();  // 可能会抛出 NullPointerException
            int result = 10 / 0;        // 可能会抛出 ArithmeticException
        } catch (NullPointerException e) {
            System.out.println("Null pointer exception: " + e.getMessage());
        } catch (ArithmeticException e) {
            System.out.println("Arithmetic exception: " + e.getMessage());
        }
    }
}
```

### 5. **`finally` 块**
`finally` 块用于执行那些无论是否发生异常都需要执行的代码（例如，关闭文件流、释放资源等）。`finally` 块是可选的，但如果存在，则无论是否抛出异常，`finally` 块中的代码都会执行。

#### 示例：
```java
public class FinallyExample {
    public static void main(String[] args) {
        try {
            System.out.println("In try block");
            int result = 10 / 0;  // 会抛出异常
        } catch (ArithmeticException e) {
            System.out.println("Caught exception: " + e.getMessage());
        } finally {
            System.out.println("Finally block executed");
        }
    }
}
```
输出：
```
In try block
Caught exception: / by zero
Finally block executed
```

### 6. **`throw` 语句**
`throw` 用于显式地抛出异常。可以在代码中主动抛出异常，传递给 `catch` 块或上层方法进行处理。

#### 示例：
```java
public class ThrowExample {
    public static void main(String[] args) {
        try {
            checkAge(15);
        } catch (IllegalArgumentException e) {
            System.out.println("Caught exception: " + e.getMessage());
        }
    }

    static void checkAge(int age) {
        if (age < 18) {
            throw new IllegalArgumentException("Age must be 18 or older");
        }
        System.out.println("Age is valid");
    }
}
```

### 7. **`throws` 关键字**
`throws` 关键字用于方法声明中，表示该方法可能抛出异常，调用该方法的地方需要处理这些异常。`throws` 适用于受检查异常。

#### 示例：
```java
public class ThrowsExample {
    public static void main(String[] args) {
        try {
            readFile();
        } catch (IOException e) {
            System.out.println("Caught exception: " + e.getMessage());
        }
    }

    static void readFile() throws IOException {
        throw new IOException("File not found");
    }
}
```

### 8. **自定义异常**
Java 允许开发者创建自己的异常类。自定义异常类需要继承 `Exception` 或 `RuntimeException`。

#### 示例：自定义异常类
```java
public class CustomExceptionExample {
    public static void main(String[] args) {
        try {
            throw new MyCustomException("This is a custom exception");
        } catch (MyCustomException e) {
            System.out.println("Caught exception: " + e.getMessage());
        }
    }
}

class MyCustomException extends Exception {
    public MyCustomException(String message) {
        super(message);
    }
}
```

### 9. **异常链**
Java 允许通过构造 `Throwable` 的 `cause` 参数，创建异常链。异常链可以追踪一个异常是如何由另一个异常引起的。

#### 示例：
```java
public class ExceptionChainExample {
    public static void main(String[] args) {
        try {
            method1();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    static void method1() throws Exception {
        try {
            method2();
        } catch (IOException e) {
            throw new Exception("Exception in method1", e);  // 抛出新异常并附带原始异常
        }
    }

    static void method2() throws IOException {
        throw new IOException("IO exception in method2");
    }
}
```
。