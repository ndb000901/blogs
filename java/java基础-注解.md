# java基础-注解

### Java 注解（Annotation）详解

**注解（Annotation）** 是 Java 中的一种特殊语法，它为程序提供元数据（metadata）。注解本身不直接影响程序的逻辑，但可以被工具、编译器或框架使用，用于生成代码、进行检查或提供配置等。

Java 提供了一组内建的注解（如 `@Override`），还允许开发者自定义注解。

---

## 1. **注解的定义与语法**
在 Java 中，注解使用 `@` 符号来定义，通常用于类、方法、字段、参数等地方。

### **示例：定义一个简单的注解**
```java
// 定义一个简单的注解
public @interface MyAnnotation {
    String value() default "default value";  // 注解中的元素，类似字段
}
```

### **使用自定义注解**
```java
@MyAnnotation(value = "Hello, Annotation!")
public class MyClass {
    // 类的定义
}
```

### **注解的元素**
注解可以有一个或多个元素（类似字段）。这些元素定义了注解的属性。注解的元素是通过方法定义的，方法名就是注解元素的名字，返回类型是元素的类型。如果元素没有指定默认值，则在使用注解时必须提供相应的值。

#### 语法：
```java
public @interface MyAnnotation {
    String name();   // 无默认值，必须在使用注解时指定
    int count() default 1; // 有默认值，可以不指定
}
```

---

## 2. **内建注解**
Java 提供了一些常用的内建注解，常见的有：

- **`@Override`**：用于标记方法重写，编译器会检查是否正确重写了父类的方法。
- **`@Deprecated`**：表示某个方法、类或字段不推荐使用，编译器会发出警告。
- **`@SuppressWarnings`**：抑制编译器的警告提示。
- **`@FunctionalInterface`**：用于标识一个接口是函数式接口，接口只能有一个抽象方法。

#### 示例：
```java
public class MyClass {
    @Override
    public String toString() {
        return "MyClass";
    }

    @Deprecated
    public void oldMethod() {
        System.out.println("This method is deprecated");
    }
}
```

---

## 3. **元注解（Meta-annotations）**
Java 还提供了一些用于注解的注解，称为**元注解（Meta-annotations）**，这些元注解用于定义其他注解的行为。

常用的元注解有：

- **`@Retention`**：定义注解的生命周期。生命周期有三种：
  - `SOURCE`：注解仅在源代码中存在，编译后丢失（通常用于代码检查、生成文档等）。
  - `CLASS`：注解在编译后仍然存在，但运行时不可用（默认值）。
  - `RUNTIME`：注解在运行时仍然存在，可以通过反射机制读取。

- **`@Target`**：指定注解可以应用的Java元素类型（如类、方法、字段、参数等）。
  - 常用值：`TYPE`（类、接口、枚举）、`METHOD`（方法）、`FIELD`（字段）、`PARAMETER`（方法参数）等。

- **`@Documented`**：表示该注解应该包含在 Javadoc 文档中。

- **`@Inherited`**：表示该注解可以被子类继承。

#### 示例：
```java
// 定义一个注解，生命周期为 RUNTIME，且可应用于方法上
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyMethodAnnotation {
    String description() default "No description";
}
```

---

## 4. **注解的使用场景**
注解的使用场景非常广泛，主要包括以下几个方面：

### 4.1 **编译时检查**
- **`@Override`** 用于标识方法重写。
- **`@Deprecated`** 用于标识不推荐使用的方法，编译器会发出警告。

### 4.2 **文档生成**
- **`@Documented`** 用于生成 Javadoc。

### 4.3 **代码生成与自动化**
- 注解可以被工具和框架（如 Spring、Hibernate）用来自动生成代码或执行某些行为。

### 4.4 **运行时反射**
通过反射机制读取注解信息，可以实现自定义的注解驱动框架。例如，Spring 框架使用注解来自动注入依赖。

---

## 5. **通过反射读取注解**
注解在运行时可以通过 Java 反射机制进行读取，通常通过 `getAnnotation()` 方法来获取注解实例。

### **示例：反射读取注解**
```java
import java.lang.annotation.*;
import java.lang.reflect.Method;

// 自定义注解
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface MyAnnotation {
    String value() default "Hello";
}

// 使用自定义注解
public class MyClass {
    @MyAnnotation(value = "Custom Annotation")
    public void myMethod() {
        System.out.println("Method executed");
    }

    public static void main(String[] args) throws Exception {
        Method method = MyClass.class.getMethod("myMethod");

        // 获取方法上的注解
        if (method.isAnnotationPresent(MyAnnotation.class)) {
            MyAnnotation annotation = method.getAnnotation(MyAnnotation.class);
            System.out.println("Annotation value: " + annotation.value());
        }
    }
}
```
**输出：**
```
Annotation value: Custom Annotation
```

---


