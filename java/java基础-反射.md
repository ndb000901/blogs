# java基础-反射

**反射（Reflection）** 是 Java 语言中的一个强大特性，它允许程序在运行时动态获取类的信息（如字段、方法、构造函数等），甚至可以创建对象、调用方法或修改字段值。

反射的核心在于 `java.lang.reflect` 包，它提供了 `Class`、`Method`、`Field`、`Constructor` 等类，支持运行时操作类的结构。

---

## **1. 反射的基本概念**
反射的核心在于 `Class` 对象，每个 Java 类在运行时都有一个 `Class` 实例，该实例用于存储类的元数据（如类名、字段、方法、构造函数等）。

| 反射类 | 说明 |
|--------|------|
| `Class<?>` | 代表 Java 类，获取类的信息 |
| `Method` | 代表方法，可用于调用方法 |
| `Field` | 代表字段，可用于获取/修改字段值 |
| `Constructor` | 代表构造函数，可用于创建对象 |

---

## **2. 获取 `Class` 对象**
在 Java 反射中，获取 `Class` 对象是所有反射操作的起点，有三种常见方式：

```java
// 方式1：使用 Class.forName()（适用于全限定类名）
Class<?> clazz1 = Class.forName("java.lang.String");

// 方式2：使用 类名.class（适用于已知类型）
Class<?> clazz2 = String.class;

// 方式3：使用对象实例的 getClass() 方法（适用于运行时对象）
String str = "Hello";
Class<?> clazz3 = str.getClass();
```

---

## **3. 通过反射创建对象**
使用 `Class` 对象可以动态创建实例：

```java
// 获取 Class 对象
Class<?> clazz = Class.forName("java.util.ArrayList");

// 使用 newInstance() 创建对象（默认调用无参构造）
Object obj = clazz.getDeclaredConstructor().newInstance();
System.out.println(obj.getClass().getName()); // 输出：java.util.ArrayList
```

> **注意**：`clazz.getDeclaredConstructor().newInstance()` 推荐使用，而 `clazz.newInstance()` 过时了，因为后者无法调用私有构造函数。

---

## **4. 通过反射操作字段（Field）**
### **4.1 获取字段**
```java
import java.lang.reflect.Field;

class Person {
    public String name;
    private int age;
}

public class ReflectionDemo {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Person.class;

        // 获取 public 字段
        Field nameField = clazz.getField("name");

        // 获取 private 字段（需要 setAccessible(true)）
        Field ageField = clazz.getDeclaredField("age");
        ageField.setAccessible(true);
    }
}
```

### **4.2 读取字段值**
```java
Person p = new Person();
p.name = "John";

// 获取 name 字段的值
System.out.println(nameField.get(p));  // 输出：John

// 访问 private 字段 age
ageField.set(p, 25);
System.out.println(ageField.get(p));   // 输出：25
```

---

## **5. 通过反射调用方法（Method）**
### **5.1 获取方法**
```java
import java.lang.reflect.Method;

class Person {
    public void sayHello() {
        System.out.println("Hello!");
    }

    private int getAge() {
        return 30;
    }
}

public class ReflectionDemo {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Person.class;
        Object obj = clazz.getDeclaredConstructor().newInstance();

        // 获取 public 方法
        Method sayHelloMethod = clazz.getMethod("sayHello");

        // 获取 private 方法
        Method getAgeMethod = clazz.getDeclaredMethod("getAge");
        getAgeMethod.setAccessible(true);
    }
}
```

### **5.2 调用方法**
```java
// 调用 public 方法
sayHelloMethod.invoke(obj); // 输出：Hello!

// 调用 private 方法
int age = (int) getAgeMethod.invoke(obj);
System.out.println(age); // 输出：30
```

---

## **6. 通过反射调用构造函数（Constructor）**
```java
import java.lang.reflect.Constructor;

class Person {
    private String name;

    public Person() {
        this.name = "Default";
    }

    public Person(String name) {
        this.name = name;
    }

    public void sayHello() {
        System.out.println("Hello, " + name);
    }
}

public class ReflectionDemo {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Person.class;

        // 获取无参构造函数
        Constructor<?> noArgsConstructor = clazz.getConstructor();
        Object obj1 = noArgsConstructor.newInstance();
        ((Person) obj1).sayHello(); // 输出：Hello, Default

        // 获取有参构造函数
        Constructor<?> constructor = clazz.getConstructor(String.class);
        Object obj2 = constructor.newInstance("Alice");
        ((Person) obj2).sayHello(); // 输出：Hello, Alice
    }
}
```

---

## **7. 判断类的父类与接口**
```java
Class<?> clazz = ArrayList.class;

// 获取父类
Class<?> superClass = clazz.getSuperclass();
System.out.println(superClass.getName()); // 输出：java.util.AbstractList

// 获取实现的接口
Class<?>[] interfaces = clazz.getInterfaces();
for (Class<?> i : interfaces) {
    System.out.println(i.getName());
}
```

---

## **8. 动态代理（应用场景）**
Java 反射常用于**动态代理**，Spring AOP（面向切面编程）大量依赖动态代理。

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

interface HelloService {
    void sayHello();
}

// 真实对象
class HelloServiceImpl implements HelloService {
    public void sayHello() {
        System.out.println("Hello, world!");
    }
}

// 动态代理类
class MyInvocationHandler implements InvocationHandler {
    private Object target;

    public MyInvocationHandler(Object target) {
        this.target = target;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before method call...");
        Object result = method.invoke(target, args);
        System.out.println("After method call...");
        return result;
    }
}

public class ProxyDemo {
    public static void main(String[] args) {
        // 创建真实对象
        HelloService target = new HelloServiceImpl();

        // 创建代理对象
        HelloService proxy = (HelloService) Proxy.newProxyInstance(
            target.getClass().getClassLoader(),
            target.getClass().getInterfaces(),
            new MyInvocationHandler(target)
        );

        // 调用代理方法
        proxy.sayHello();
    }
}
```
**输出：**
```
Before method call...
Hello, world!
After method call...
```

---

## **9. 反射的缺点**
1. **性能开销**：反射比直接调用慢，因为需要解析字节码并进行安全检查。
2. **安全性**：反射可以访问私有字段，可能导致数据泄露。
3. **代码可读性**：使用反射的代码较难阅读和维护。
