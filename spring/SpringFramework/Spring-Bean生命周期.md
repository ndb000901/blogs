# Spring-Bean生命周期

## 1.依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.2.3</version>
</dependency>
```

## 2.Bean的生命周期

- 无参构造函数
- setter方法
- postProcessBeforeInitialization(对所有bean都生效)
- init方法
- postProcessAfterInitialization(对所有bean都生效)
- 使用bean
- destroy方法



## 3.源码

`BeanLifeDemo.java`
```java
public class BeanLifeDemo {

    public static void main(String[] args) {
        ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("bean-life.xml");
        User3 user1 = (User3) context.getBean("user1");

        System.out.println(user1.toString());
        context.close();

    }
}

```

`User3.java`
```java
public class User3 {

    private String name;


    public String getName() {
        System.out.println("User3 getName");
        return name;
    }

    public void setName(String name) {
        this.name = name;
        System.out.println("User3 setName");
    }


    public User3(String name) {
        this.name = name;
        System.out.println("User3 有参构造函数");
    }

    public User3() {
        System.out.println("User3 无参构造函数");
    }

    @Override
    public String toString() {
        return "User3{" +
                "name='" + name + '\'' +
                '}';
    }

    public void initMethod() {
        System.out.println("init method run...");
    }

    public void destroyMethod() {
        System.out.println("destroy method run...");
    }
}

```


`BeanProcessor.java`
```java

public class BeanProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessAfterInitialization...");
        return BeanPostProcessor.super.postProcessAfterInitialization(bean, beanName);
    }

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessBeforeInitialization...");
        return BeanPostProcessor.super.postProcessBeforeInitialization(bean, beanName);
    }
}

```

`bean-life.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user1" class="com.hello.demo.spring.bean.xml.common.User3"
    init-method="initMethod" destroy-method="destroyMethod">
        <property name="name" value="user1"/>
    </bean>

    <!-- bean的后置处理器要放入IOC容器才能生效 -->
    <bean id="beanProcessor" class="com.hello.demo.spring.bean.xml.common.BeanProcessor"/>
</beans>
```

### 输出结果

```text
2025-02-23 15:12:18 [main] DEBUG org.springframework.context.support.ClassPathXmlApplicationContext - Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@1ab3a8c8
2025-02-23 15:12:18 [main] DEBUG org.springframework.beans.factory.xml.XmlBeanDefinitionReader - Loaded 2 bean definitions from class path resource [bean-life.xml]
2025-02-23 15:12:18 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'beanProcessor'
2025-02-23 15:12:18 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'user1'
User3 无参构造函数
User3 setName
postProcessBeforeInitialization...
init method run...
postProcessAfterInitialization...
User3{name='user1'}
2025-02-23 15:12:18 [main] DEBUG org.springframework.context.support.ClassPathXmlApplicationContext - Closing org.springframework.context.support.ClassPathXmlApplicationContext@1ab3a8c8, started on Sun Feb 23 15:12:18 UTC 2025
destroy method run...
2025-02-23 15:12:18 [main] DEBUG org.springframework.beans.factory.support.DisposableBeanAdapter - Custom destroy method 'destroyMethod' on bean with name 'user1' completed

Process finished with exit code 0
```