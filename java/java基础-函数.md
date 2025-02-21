# java基础-函数

在 Java 中，**函数**（也称为**方法**）是具有一组特定功能的代码块。函数是 Java 编程语言的基本构建块之一，它可以接收输入、执行某些操作并返回结果。Java 方法通常用于执行特定的操作，并可以通过不同的参数和返回类型进行定制。

### 1. **方法的基本语法**
Java 方法的基本语法结构如下：

```java
返回类型 方法名(参数列表) {
    // 方法体
    return 返回值;  // 可选，根据返回类型决定
}
```

#### 示例：
```java
public int add(int a, int b) {
    return a + b;
}
```

### 2. **方法的组成部分**
每个方法由以下几个主要部分组成：

- **返回类型**：指定方法执行完毕后返回的值的类型。如果方法没有返回值，则使用 `void`。
- **方法名**：标识方法的名称，按照命名规则命名。
- **参数列表**：方法接收的输入数据（可选）。可以没有参数，或者有多个参数。
- **方法体**：方法的具体实现代码，定义了方法的行为。

### 3. **方法的分类**
- **实例方法（Non-static methods）**：依赖于类的实例化对象，必须通过对象调用。
- **静态方法（Static methods）**：不依赖于类的实例，可以通过类名直接调用。

### 4. **方法的返回类型**
方法可以有返回值，也可以没有返回值。返回类型的选择基于方法的功能需求。

#### 4.1 **无返回值方法（`void` 返回类型）**
如果方法没有返回值，则使用 `void`。

```java
public void printMessage() {
    System.out.println("Hello, World!");
}
```

#### 4.2 **有返回值方法**
如果方法需要返回某个值，则需要指定相应的返回类型。

```java
public int add(int a, int b) {
    return a + b;
}
```

### 5. **方法的参数**
方法可以通过参数接收外部传递的数据。参数在方法定义时声明，方法调用时传递实际参数。

#### 示例：
```java
public void greet(String name) {
    System.out.println("Hello, " + name);
}

public static void main(String[] args) {
    greet("Alice");  // 输出 "Hello, Alice"
}
```

#### 5.1 **参数类型**
参数可以是基本数据类型（如 `int`、`char` 等），也可以是引用数据类型（如对象、数组等）。

#### 5.2 **可变参数（Varargs）**
Java 允许在方法中使用可变参数（`varargs`），这允许传递多个相同类型的参数，而无需显式地定义多个参数。

```java
public void printNumbers(int... numbers) {
    for (int num : numbers) {
        System.out.println(num);
    }
}

public static void main(String[] args) {
    printNumbers(1, 2, 3, 4);  // 输出 1 2 3 4
}
```

### 6. **静态方法和实例方法**
- **实例方法**：需要通过类的实例来调用。
  
```java
public class MyClass {
    public void greet() {
        System.out.println("Hello from instance method!");
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.greet();  // 通过对象调用实例方法
    }
}
```

- **静态方法**：可以直接通过类名调用，不需要实例化对象。

```java
public class MyClass {
    public static void greet() {
        System.out.println("Hello from static method!");
    }

    public static void main(String[] args) {
        MyClass.greet();  // 直接通过类名调用静态方法
    }
}
```

### 7. **方法的重载**
方法重载是指在同一类中，可以定义多个具有相同方法名但参数不同的方法。方法的返回类型可以相同，也可以不同，但不能仅根据返回类型来区分重载方法。

#### 示例：
```java
public class Calculator {
    // 加法方法，接收两个整数
    public int add(int a, int b) {
        return a + b;
    }

    // 重载的加法方法，接收两个浮点数
    public double add(double a, double b) {
        return a + b;
    }

    public static void main(String[] args) {
        Calculator calc = new Calculator();
        System.out.println(calc.add(2, 3));         // 输出 5
        System.out.println(calc.add(2.5, 3.5));     // 输出 6.0
    }
}
```

### 8. **方法的重写（Override）**
方法重写是指在子类中重新定义父类的方法，以改变父类方法的实现。重写方法必须保持与父类方法相同的名称、返回类型和参数列表。

#### 示例：
```java
class Animal {
    public void sound() {
        System.out.println("Animal makes a sound");
    }
}

class Dog extends Animal {
    @Override
    public void sound() {
        System.out.println("Dog barks");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal myAnimal = new Animal();
        myAnimal.sound();  // 输出 Animal makes a sound
        
        Animal myDog = new Dog();
        myDog.sound();  // 输出 Dog barks
    }
}
```

### 9. **递归函数**
递归是指函数在其实现中调用自身。递归通常用于解决分治类型的问题，如阶乘、斐波那契数列等。

#### 示例：计算阶乘
```java
public class Factorial {
    public int factorial(int n) {
        if (n == 0) {
            return 1;  // 递归基准
        }
        return n * factorial(n - 1);  // 递归调用
    }

    public static void main(String[] args) {
        Factorial f = new Factorial();
        System.out.println(f.factorial(5));  // 输出 120
    }
}
```

### 10. **方法的访问修饰符**
方法的访问控制通过修饰符来决定。常用的访问修饰符有：
- `public`：方法可以被任何类访问。
- `private`：方法只能在当前类中访问。
- `protected`：方法在当前类、同一包中的类和子类中可以访问。
- `default`（无修饰符）：方法在同一包中的类可以访问。

