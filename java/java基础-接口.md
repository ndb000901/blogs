# java基础-接口

在 Java 中，**接口**（`interface`）是一种特殊的引用类型，它定义了一组方法，但不提供这些方法的实现。接口主要用于规定类必须实现的行为，是 Java 中实现多重继承和松耦合设计的一个重要机制。类通过实现接口来继承接口的方法，并提供这些方法的具体实现。

## 1. **接口的定义和声明**

接口的定义类似于类的定义，使用 `interface` 关键字进行声明。接口不能包含方法的实现（除非是默认方法或静态方法）。所有方法默认都是 `public` 和 `abstract`，因此可以省略 `public` 和 `abstract` 修饰符。

### 语法：
```java
public interface InterfaceName {
    // 声明常量（默认是 public static final）
    int CONSTANT = 100;

    // 声明抽象方法（默认是 public abstract）
    void method1();
    int method2(int param);
}
```

### 示例：
```java
public interface Animal {
    void makeSound();  // 抽象方法，不提供实现
    void eat();        // 抽象方法
}
```

## 2. **接口的实现**

类使用 `implements` 关键字来实现接口。实现接口的类必须提供接口中所有抽象方法的实现。

### 语法：
```java
public class ClassName implements InterfaceName {
    // 实现接口中的所有抽象方法
    public void method1() {
        // 方法实现
    }
}
```

### 示例：
```java
public class Dog implements Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }

    @Override
    public void eat() {
        System.out.println("Dog is eating");
    }
}
```

## 3. **接口的多继承**

Java 允许一个类实现多个接口，这样可以避免单继承模型中的限制，实现多重继承的效果。

### 示例：
```java
public interface Walkable {
    void walk();
}

public interface Swimable {
    void swim();
}

public class Dog implements Walkable, Swimable {
    @Override
    public void walk() {
        System.out.println("Dog is walking");
    }

    @Override
    public void swim() {
        System.out.println("Dog is swimming");
    }
}
```

## 4. **接口中的默认方法（`default`）**

从 Java 8 开始，接口可以包含带有实现的默认方法。默认方法使用 `default` 关键字声明，允许接口提供方法的实现，这对于向现有接口添加新功能而不破坏现有实现非常有用。

### 示例：
```java
public interface Animal {
    void makeSound();

    // 默认方法
    default void sleep() {
        System.out.println("Animal is sleeping");
    }
}
```

实现该接口的类可以选择覆盖该默认方法，或者直接使用接口中的默认实现。

### 示例：使用默认方法
```java
public class Dog implements Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }

    // Dog 类使用默认的 sleep 方法，无需重写
}
```

## 5. **接口中的静态方法（`static`）**

接口还可以定义静态方法，静态方法属于接口本身，可以通过接口名来调用，不能通过实现类来调用。

### 示例：
```java
public interface Animal {
    static void printInfo() {
        System.out.println("This is an Animal interface.");
    }
}

public class Main {
    public static void main(String[] args) {
        // 通过接口名调用静态方法
        Animal.printInfo();
    }
}
```

## 6. **接口中的常量**

在接口中定义的变量默认是 `public static final`，即常量。常量的值必须在定义时赋值，并且不能修改。

### 示例：
```java
public interface Animal {
    // 常量定义
    int MAX_AGE = 100;
}
```

## 7. **接口的继承**

接口可以继承另一个接口，继承的接口将包含所有父接口中声明的方法。

### 示例：
```java
public interface Animal {
    void eat();
}

public interface Walkable extends Animal {
    void walk();
}

public class Dog implements Walkable {
    @Override
    public void eat() {
        System.out.println("Dog is eating");
    }

    @Override
    public void walk() {
        System.out.println("Dog is walking");
    }
}
```

## 8. **接口的使用场景**

- **多重继承**：Java 不支持类的多重继承，但接口允许类实现多个接口，从而实现多重继承的效果。
- **松耦合设计**：接口有助于实现类之间的松耦合，因为接口仅定义行为，不关心具体实现，可以通过接口类型引用实现类对象。
- **回调机制**：接口常用于回调机制，特别是在事件驱动编程或异步编程中。

## 9. **接口与抽象类的区别**
虽然接口和抽象类都可以用于定义方法签名，但它们有以下不同点：

| 特性                  | 接口                            | 抽象类                        |
|-----------------------|---------------------------------|-------------------------------|
| 方法实现              | 接口中的方法默认是抽象的，Java 8 后可以有默认方法和静态方法 | 抽象类中的方法可以是抽象的，也可以有实现 |
| 继承                  | 可以实现多个接口                | 只能继承一个抽象类             |
| 构造器                | 无构造器                        | 可以有构造器                   |
| 字段                  | 只能定义常量（`public static final`） | 可以有实例字段和静态字段       |
| 访问修饰符            | 默认是 `public`，不能使用 `private` 和 `protected` | 可以使用 `public`、`protected`、`private` |
| 实现方式              | 必须使用 `implements` 关键字    | 使用 `extends` 关键字          |

### 示例：接口 vs 抽象类
```java
// 抽象类
abstract class Animal {
    abstract void sound();
}

// 接口
interface Animal {
    void sound();
}
```
