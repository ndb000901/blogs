# Java 动态代理-cglib


## 1. CGLIB 动态代理

CGLIB (Code Generation Library) 通过 **继承** 目标类并 **生成子类**，然后重写目标方法来实现动态代理。它不依赖于接口，因此可以代理 **没有实现接口的普通类**。



## 2.依赖

```xml
<!-- https://mvnrepository.com/artifact/cglib/cglib -->
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
</dependency>
```

## 2.源码

`User.java`
```java
package com.hello.demo.cglib.common;

public class User {

    private String name;
    
    private int age;

    public void sayHello() {
        System.out.println("hello");
    }
}

```

`UserProxy.java`
```java
package com.hello.demo.cglib.common;

import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class UserProxy implements MethodInterceptor {

    private Object target;

    public UserProxy(Object target) {
        this.target = target;
    }

    public Object getProxyInstance() {
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(target.getClass());
        enhancer.setCallback(this);
        return enhancer.create();
    }

    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println(method.getName() + " 方法执行前");
        Object result = methodProxy.invokeSuper(o, objects);
        System.out.println(method.getName() + " 方法执行后");
        return null;
    }
}

```

`ProxyDemo.java`
```java
package com.hello.demo.cglib;

import com.hello.demo.cglib.common.User;
import com.hello.demo.cglib.common.UserProxy;

public class ProxyDemo {
    public static void main(String[] args) {
        User user = new User();
        UserProxy userProxy = new UserProxy(user);

        User userInstance = (User)userProxy.getProxyInstance();
        userInstance.sayHello();
    }
}
```
