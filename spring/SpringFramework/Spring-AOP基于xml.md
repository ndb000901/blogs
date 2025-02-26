# Spring-AOP基于xml

## 依赖

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

## 源码

`LoggingAspect.java`
```java

package com.hello.demo.spring.aop.xml.common;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;

import java.util.Arrays;

public class LoggingAspect {


    // execution(访问修饰符  返回类型  包名.类名.方法名(参数列表) 异常类型)

    public void before(JoinPoint joinPoint) {
        System.out.println("before run...  -->" + joinPoint.getSignature().getName());
    }

    public void after(JoinPoint joinPoint) {
        System.out.println("after run...  --->" + joinPoint.getSignature().getName());
    }

    public void afterReturning(JoinPoint joinPoint, Object result) {
        System.out.println("afterReturning run...  --->" + joinPoint.getSignature().getName() + " result: " + result);
    }

    public void afterThrowing(JoinPoint joinPoint, Throwable ex) {
        System.out.println("afterThrowing run..." + ex.getMessage());
    }


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

`UserService.java`

```java
package com.hello.demo.spring.aop.xml.service;


public class UserService {

    public void getUser() {
//        int a = 1/0;
        System.out.println("UserService.getUser run...");
    }
}

```

`bean.xml`

advice xml中的配置顺序会影响执行顺序
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
	   https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="userService" class="com.hello.demo.spring.aop.xml.service.UserService"/>

    <bean id="loggingAspect" class="com.hello.demo.spring.aop.xml.common.LoggingAspect"/>

    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.hello.demo.spring.aop.xml.service.UserService.*(..))"/>
        <aop:aspect ref="loggingAspect">

            <aop:after-returning method="afterReturning" pointcut-ref="pointcut" returning="result"/>
            <aop:after-throwing method="afterThrowing" pointcut-ref="pointcut" throwing="ex"/>
            <aop:after method="after" pointcut-ref="pointcut"/>


            <!--before around顺序会影响输出顺序-->
            <aop:before method="before" pointcut-ref="pointcut"/>
            <aop:around method="around" pointcut-ref="pointcut"/>

        </aop:aspect>

    </aop:config>


</beans>
```

`AOPDemo.java`
```java

package com.hello.demo.spring.aop.xml;

import com.hello.demo.spring.aop.xml.service.UserService;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AOPDemo {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("bean.xml");
        UserService userService = (UserService) context.getBean("userService");
        userService.getUser();
    }
}
```

