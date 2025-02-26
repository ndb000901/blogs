# Spriong FactoryBean使用


`FactoryBean<T>` 是 Spring 提供的一种 **工厂模式**（Factory Pattern）接口，它允许我们 **创建复杂的 Bean**，而不仅仅是直接实例化某个类。

- **普通 Bean**：Spring 直接实例化对象
- **`FactoryBean`**：Spring 调用 `FactoryBean#getObject()` 方法 **返回** 一个对象

## **1. `FactoryBean` 主要用途**
1. **创建复杂 Bean**（如代理对象、线程池、数据库连接）
2. **动态代理 Bean**（如 AOP 代理）
3. **生成缓存对象**（如 Redis 连接）
4. **延迟初始化 Bean**（如大对象的按需加载）

---

## **2. `FactoryBean` 工作原理**
Spring 在创建 Bean 时，如果发现某个 Bean 实现了 `FactoryBean` 接口：
- **调用 `getObject()` 方法** 获取 Bean 实例，而不是直接实例化该类
- 通过 **`isSingleton()` 方法** 确定是否使用单例
- 可以通过 `&beanName` 获取 `FactoryBean` 本身

---

## **3. 典型应用场景**
- **MyBatis**：`SqlSessionFactoryBean` 创建 `SqlSessionFactory`
- **Spring AOP**：使用 `ProxyFactoryBean` 创建代理对象
- **Spring ORM**：如 `LocalSessionFactoryBean` 创建 Hibernate `SessionFactory`
- **Redis**：`JedisConnectionFactory` 用于 Redis 连接池
- **ElasticSearch**：使用 `TransportClientFactoryBean` 创建客户端

## **4. 示例源码**

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

`UserFactoryBean.java`
```java
package com.hello.demo.spring.bean.xml.common;


import org.springframework.beans.factory.FactoryBean;

public class UserFactoryBean implements FactoryBean<User3> {

    @Override
    public User3 getObject() throws Exception {
        User3 user = new User3();
        user.setName("user1-->factoryBean");
        System.out.println("UserBeanFactory.getObject run...");
        return user;
    }

    @Override
    public Class<?> getObjectType() {
        return User3.class;
    }

    @Override
    public boolean isSingleton() {
        return FactoryBean.super.isSingleton();
    }
}

```

`FactoryBeanDemo.java`
```java

public class FactoryBeanDemo {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("bean-factory.xml");
        User3 user1 = (User3)context.getBean("userFactoryBean");
        UserFactoryBean userFactoryBean = (UserFactoryBean)context.getBean("&userFactoryBean");
        System.out.println(user1.toString());
        System.out.println(userFactoryBean.toString());
    }
}

```

`bean-factory.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="userFactoryBean" class="com.hello.demo.spring.bean.xml.common.UserFactoryBean" />
</beans>
```

### 输出结果

```text
2025-02-23 15:52:56 [main] DEBUG org.springframework.context.support.ClassPathXmlApplicationContext - Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@6eda5c9
2025-02-23 15:52:56 [main] DEBUG org.springframework.beans.factory.xml.XmlBeanDefinitionReader - Loaded 1 bean definitions from class path resource [bean-factory.xml]
2025-02-23 15:52:56 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'userFactoryBean'
User3 无参构造函数
User3 setName
UserBeanFactory.getObject run...
User3{name='user1-->factoryBean'}
com.hello.demo.spring.bean.xml.common.UserFactoryBean@194fad1
```