# Java 动态代理

动态代理是一种在运行时创建代理类的机制，允许在不修改原始代码的情况下增强方法的行为。Java 提供了两种方式实现动态代理：
- **JDK 动态代理（基于 `java.lang.reflect.Proxy` 和 `InvocationHandler`）**
- **CGLIB 动态代理（基于子类继承，使用 ASM 字节码技术）**

JDK 动态代理只能代理 **实现了接口** 的类，而 CGLIB 可以代理 **没有实现接口的普通类**。


## 2. JDK 动态代理

JDK 动态代理通过 **反射** 机制创建代理类，并在代理类中通过 `InvocationHandler` 进行方法拦截。代理对象并不是目标对象的子类，而是实现了相同的接口。



## 3.源码

`SayHello.java`
```java
package com.hello.demo.reflect.common;

public interface SayHello {

    void sayHello();
}

```

`User.java`
```java
package com.hello.demo.reflect.common;

public class User implements SayHello {

    private String name;

    private int age;

    public User() {
    }

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public void sayHello() {
        System.out.println("hello");
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

`UserProxy.java`
```java
package com.hello.demo.reflect.common;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class UserProxy implements InvocationHandler {

    private Object target;

    public UserProxy(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        System.out.println(method.getName() + " 执行前");
        Object result = method.invoke(target, args);
        System.out.println(method.getName() + " 执行后");
        return result;
    }
}

```

`ProxyDemo.java`
```java
package com.hello.demo.reflect;

import com.hello.demo.reflect.common.SayHello;
import com.hello.demo.reflect.common.User;
import com.hello.demo.reflect.common.UserProxy;

import java.lang.reflect.Proxy;

public class ProxyDemo {

    public static void main(String[] args) {

        SayHello user = new User();

        SayHello proxy = (SayHello)Proxy.newProxyInstance(
                user.getClass().getClassLoader(),
                user.getClass().getInterfaces(),
                new UserProxy(user)
        );
        proxy.sayHello();
    }
}

```

### 输出

```text
sayHello 执行前
hello
sayHello 执行后
```