# Spring-AOP基于注解

Spring AOP（Aspect-Oriented Programming，面向切面编程）是 Spring 框架提供的一个重要特性，用于在不修改代码的情况下增强 Spring Bean 的功能。它通过**动态代理**或**字节码增强**，在方法执行的不同阶段（如调用前、调用后、异常抛出后等）插入额外的行为（切面逻辑）。

---

## 1. AOP 的核心概念
Spring AOP 主要基于**代理模式**，并依赖于 Spring 容器的**IoC（控制反转）机制**来织入（Weaving）切面逻辑。


| 术语 | 说明 |
|------|------|
| **Aspect（切面）** | 由多个**通知（Advice）**和**切点（Pointcut）**组成，定义了要增强的功能和位置。 |
| **Advice（通知）** | 切面中的具体增强代码，如方法执行前后执行的逻辑。 |
| **JoinPoint（连接点）** | 代码执行的某个点，如方法调用、对象实例化等，Spring AOP 仅支持方法级别的连接点。 |
| **Pointcut（切点）** | 用于定义**在哪些连接点**应用通知，通常使用表达式定义（如 `execution(* com.example.service.*.*(..))`）。 |
| **Target（目标对象）** | 被代理的对象，即增强功能要应用到的对象。 |
| **Proxy（代理对象）** | 增强后的对象，Spring AOP 通过**JDK 动态代理**或**CGLIB**生成。 |
| **Weaving（织入）** | 将切面逻辑（Aspect）应用到目标对象的过程，Spring 在运行时完成织入（动态织入）。 |

---

## 2. Spring AOP 的实现方式
Spring AOP 主要依赖**动态代理**来实现：

1. **JDK 动态代理（基于接口）**：
   - 适用于**目标对象实现了接口**的情况。
   - 通过 `java.lang.reflect.Proxy` 创建代理对象。
  
2. **CGLIB 动态代理（基于子类）**：
   - 适用于**目标对象没有实现接口**的情况。
   - 通过**继承**目标类生成代理类（依赖于 `cglib` 库）。



## 3. AOP 切点表达式详解

格式：
```plaintext
execution(访问修饰符  返回类型  包名.类名.方法名(参数列表) 异常类型)
```

示例：

| 表达式 | 说明 |
|--------|------|
| `execution(* com.example.service.UserService.getUser())` | 匹配 `UserService.getUser()` 方法 |
| `execution(* com.example.service.*.*(..))` | 匹配 `service` 包下所有类的所有方法 |
| `execution(public * *(..))` | 匹配所有 `public` 方法 |

---


## 4.源码

`pom.yml` 依赖
```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>6.2.3</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-aop -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>6.2.3</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.springframework/spring-aspects -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>6.2.3</version>
    </dependency>
</dependencies>

```

`AppConfig.java` 开启注解扫描与启用AOP
```java
package com.hello.demo.spring.aop.annotation.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@ComponentScan("com.hello")
@EnableAspectJAutoProxy
public class AppConfig {
}

```

`UserService.java`
```java
package com.hello.demo.spring.aop.annotation.service;

import org.springframework.stereotype.Service;

@Service
public class UserService {

    public void getUser() {
//        int a = 1/0;
        System.out.println("UserService.getUser run...");
    }
}


```

`LoggingAspect.java`
```java

package com.hello.demo.spring.aop.annotation.common;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

import javax.xml.stream.events.ProcessingInstruction;
import java.util.Arrays;

@Aspect
@Component
public class LoggingAspect {

    @Pointcut("execution(* com.hello.demo.spring.aop.annotation.service.UserService.*(..))")
    public void getPointcut() {

    }
    // execution(访问修饰符  返回类型  包名.类名.方法名(参数列表) 异常类型)

    @Before("execution(public * com.hello.demo.spring.aop.annotation.service.UserService.*(..))")
    public void before(JoinPoint joinPoint) {
        System.out.println("before run...  -->" + joinPoint.getSignature().getName());
    }

    @After("execution(* com.hello.demo.spring.aop.annotation.service.UserService.*(..))")
    public void after(JoinPoint joinPoint) {
        System.out.println("after run...  --->" + joinPoint.getSignature().getName());
    }

    @AfterReturning(
            value = "execution(* com.hello.demo.spring.aop.annotation.service.UserService.*(..))",
            returning = "result"
    )
    public void afterReturning(JoinPoint joinPoint, Object result) {
        System.out.println("afterReturning run...  --->" + joinPoint.getSignature().getName() + " result: " + result);
    }

    @AfterThrowing(
            value = "execution(* com.hello.demo.spring.aop.annotation.service.UserService.*(..))",
            throwing = "ex"
    )
    public void afterThrowing(JoinPoint joinPoint, Throwable ex) {
        System.out.println("afterThrowing run..." + ex.getMessage());
    }

    // 从Pointcut方法获取
    @Around("getPointcut()")
    public Object around(ProceedingJoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        String args = Arrays.toString(joinPoint.getArgs());
        Object result = null;
        try {
            System.out.println("around-->目标对象方法执行之前");
            //目标对象（连接点）方法的执行
            result = joinPoint.proceed();
            System.out.println("around-->目标对象方法返回值之后");
        } catch (Throwable e) {
            System.out.println("around-->目标对象方法出现异常时 -->" + e.getMessage());
        } finally {
            System.out.println("around-->目标对象方法执行完毕");
        }
        return result;
    }
}


```

`AOPDemo.java`
```java
package com.hello.demo.spring.aop.annotation;

import com.hello.demo.spring.aop.annotation.config.AppConfig;
import com.hello.demo.spring.aop.annotation.service.UserService;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class AOPDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);
        UserService userService = context.getBean(UserService.class);
        userService.getUser();
    }
}

```

正常输出

```text
around-->目标对象方法执行之前
before run...  -->getUser
UserService.getUser run...
afterReturning run...  --->getUser result: null
after run...  --->getUser
around-->目标对象方法返回值之后
around-->目标对象方法执行完毕
```

异常输出

around 包裹了整个方法(目标方法+其他四类Advice)

```text
around-->目标对象方法执行之前
before run...  -->getUser
afterThrowing run.../ by zero
after run...  --->getUser
around-->目标对象方法出现异常时 -->/ by zero
around-->目标对象方法执行完毕
```

### 切面优先级

`@Order(1)` 控制优先级，数字小的优先级高

`LoggingAspect.java`
```java
package com.hello.demo.spring.aop.annotation.common;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

import javax.xml.stream.events.ProcessingInstruction;
import java.util.Arrays;

@Aspect
@Component
public class LoggingAspect {

    @Pointcut("execution(* com.hello.demo.spring.aop.annotation.service.UserService.*(..))")
    public void getPointcut() {

    }
    // execution(访问修饰符  返回类型  包名.类名.方法名(参数列表) 异常类型)

    @Order(5) // 数字小的优先级高
    @Before("execution(public * com.hello.demo.spring.aop.annotation.service.UserService.*(..))")
    public void before(JoinPoint joinPoint) {
        System.out.println("before run...  -->" + joinPoint.getSignature().getName());
    }

    @Order(10)
    @Before("execution(public * com.hello.demo.spring.aop.annotation.service.UserService.*(..))")
    public void before2(JoinPoint joinPoint) {
        System.out.println("before2 run...  -->" + joinPoint.getSignature().getName());
    }

    @After("execution(* com.hello.demo.spring.aop.annotation.service.UserService.*(..))")
    public void after(JoinPoint joinPoint) {
        System.out.println("after run...  --->" + joinPoint.getSignature().getName());
    }

    @AfterReturning(
            value = "execution(* com.hello.demo.spring.aop.annotation.service.UserService.*(..))",
            returning = "result"
    )
    public void afterReturning(JoinPoint joinPoint, Object result) {
        System.out.println("afterReturning run...  --->" + joinPoint.getSignature().getName() + " result: " + result);
    }

    @AfterThrowing(
            value = "execution(* com.hello.demo.spring.aop.annotation.service.UserService.*(..))",
            throwing = "ex"
    )
    public void afterThrowing(JoinPoint joinPoint, Throwable ex) {
        System.out.println("afterThrowing run..." + ex.getMessage());
    }

    // 从Pointcut方法获取
    @Around("getPointcut()")
    public Object around(ProceedingJoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        String args = Arrays.toString(joinPoint.getArgs());
        Object result = null;
        try {
            System.out.println("around-->目标对象方法执行之前");
            //目标对象（连接点）方法的执行
            result = joinPoint.proceed();
            System.out.println("around-->目标对象方法返回值之后");
        } catch (Throwable e) {
            System.out.println("around-->目标对象方法出现异常时 -->" + e.getMessage());
        } finally {
            System.out.println("around-->目标对象方法执行完毕");
        }
        return result;
    }
}

```

输出结果

```text
around-->目标对象方法执行之前
before run...  -->getUser
before2 run...  -->getUser
UserService.getUser run...
afterReturning run...  --->getUser result: null
after run...  --->getUser
around-->目标对象方法返回值之后
around-->目标对象方法执行完毕
```

### Advice执行顺序

[官网文档](https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html)

```text
Each of the distinct advice types of a particular aspect is conceptually meant to apply to the join point directly. As a consequence, an @AfterThrowing advice method is not supposed to receive an exception from an accompanying @After/@AfterReturning method.

Advice methods defined in the same @Aspect class that need to run at the same join point are assigned precedence based on their advice type in the following order, from highest to lowest precedence: @Around, @Before, @After, @AfterReturning, @AfterThrowing. Note, however, that an @After advice method will effectively be invoked after any @AfterReturning or @AfterThrowing advice methods in the same aspect, following AspectJ’s "after finally advice" semantics for @After.

When two pieces of the same type of advice (for example, two @After advice methods) defined in the same @Aspect class both need to run at the same join point, the ordering is undefined (since there is no way to retrieve the source code declaration order through reflection for javac-compiled classes). Consider collapsing such advice methods into one advice method per join point in each @Aspect class or refactor the pieces of advice into separate @Aspect classes that you can order at the aspect level via Ordered or @Order.
```