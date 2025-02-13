# java基础-类


### Java 类（Class）详解

在 Java 中，**类（Class）** 是面向对象编程（OOP）的核心概念。类是对象的模板或蓝图，定义了对象的属性（字段）和行为（方法）。

---

## 1. **类的基本定义**
Java 中使用 `class` 关键字来声明类，类通常包含：
- **字段（Field）**：即成员变量，表示对象的属性。
- **方法（Method）**：表示对象的行为。

### **示例：定义一个简单的类**
```java
public class Person {
    // 成员变量（字段）
    String name;
    int age;

    // 方法
    void introduce() {
        System.out.println("My name is " + name + " and I am " + age + " years old.");
    }
}
```

---

## 2. **对象的创建与使用**
Java 的类必须实例化为对象后才能使用。

### **示例：创建 `Person` 类的对象**
```java
public class Main {
    public static void main(String[] args) {
        // 创建对象
        Person p1 = new Person();
        p1.name = "John";
        p1.age = 25;

        // 调用方法
        p1.introduce();
    }
}
```
**输出：**
```
My name is John and I am 25 years old.
```

---

## 3. **构造方法（Constructor）**
构造方法用于在创建对象时初始化对象的属性，方法名必须与类名相同。

### **示例：使用构造方法**
```java
public class Person {
    String name;
    int age;

    // 构造方法
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    void introduce() {
        System.out.println("My name is " + name + " and I am " + age + " years old.");
    }
}
```

### **使用构造方法创建对象**
```java
public class Main {
    public static void main(String[] args) {
        Person p1 = new Person("Alice", 22);
        p1.introduce();
    }
}
```
**输出：**
```
My name is Alice and I am 22 years old.
```

### **构造方法的重载**
Java 允许多个构造方法，只要参数不同。

```java
public class Person {
    String name;
    int age;

    // 无参构造方法
    public Person() {
        this.name = "Unknown";
        this.age = 0;
    }

    // 带参数的构造方法
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

---

## 4. **类的访问修饰符**
| 修饰符    | 当前类 | 同包 | 子类 | 其他包 |
|----------|--------|------|------|--------|
| `public` | ✅      | ✅    | ✅    | ✅      |
| `protected` | ✅   | ✅    | ✅    | ❌      |
| **无修饰符（默认）** | ✅ | ✅    | ❌    | ❌      |
| `private` | ✅     | ❌    | ❌    | ❌      |

```java
public class Person {
    private String name;  // 仅在当前类可访问
    protected int age;    // 在当前包及子类可访问
    public String gender; // 任何地方可访问

    public void setName(String name) {
        this.name = name; // 通过公有方法访问私有变量
    }
}
```

---

## 5. **静态成员（Static）**
使用 `static` 修饰的变量或方法属于 **类本身**，而不是某个实例。

### **示例：静态变量和静态方法**
```java
public class Student {
    static String school = "ABC University"; // 静态变量
    String name;

    public Student(String name) {
        this.name = name;
    }

    static void changeSchool(String newSchool) {
        school = newSchool;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        System.out.println(Student.school); // 直接访问静态变量
        Student.changeSchool("XYZ University");
    }
}
```

---

## 6. **内部类**
Java 允许在类内部定义类，称为 **内部类（Inner Class）**。

### **示例：成员内部类**
```java
public class Outer {
    private String message = "Hello from Outer class";

    class Inner {
        void display() {
            System.out.println(message);
        }
    }
}
```

使用内部类：
```java
public class Main {
    public static void main(String[] args) {
        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner();
        inner.display();
    }
}
```

**其他内部类类型：**
- **静态内部类（static nested class）**
- **局部内部类（local inner class）**
- **匿名内部类（anonymous inner class）**

---

## 7. **继承（Inheritance）**
Java 支持单继承，一个类可以继承另一个类，使用 `extends` 关键字。

```java
public class Animal {
    void makeSound() {
        System.out.println("Animal makes a sound");
    }
}

public class Dog extends Animal {
    void bark() {
        System.out.println("Dog barks");
    }
}
```

使用：
```java
public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.makeSound(); // 继承自 Animal
        dog.bark();
    }
}
```

**输出：**
```
Animal makes a sound
Dog barks
```

---

## 8. **多态（Polymorphism）**
多态指的是相同的方法在不同类中的不同实现。

```java
class Animal {
    void makeSound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Dog barks");
    }
}
```
```java
public class Main {
    public static void main(String[] args) {
        Animal myDog = new Dog(); // 向上转型
        myDog.makeSound(); // 运行时调用 Dog 的 makeSound()
    }
}
```

**输出：**
```
Dog barks
```

---

## 9. **抽象类**
使用 `abstract` 关键字定义抽象类，抽象类不能实例化，必须由子类实现。

```java
abstract class Animal {
    abstract void makeSound(); // 抽象方法，没有实现
}
```

子类必须实现抽象方法：
```java
class Dog extends Animal {
    void makeSound() {
        System.out.println("Dog barks");
    }
}
```

---
