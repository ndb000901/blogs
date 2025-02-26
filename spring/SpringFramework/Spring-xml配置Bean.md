# Spring-xml配置Bean

## 1.依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.2.3</version>
</dependency>
```

## 2.获取Bean

```java
public class BeanDemo {
    public static void main(String[] args) {
        // setter
        ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("bean.xml");
        User user1 = (User)context.getBean("user1");
        System.out.println(user1.toString());

        // 构造函数
        User user2 = (User)context.getBean("user2");
        System.out.println(user2.toString());

        // p命名空间
        User user3 = (User)context.getBean("user3");
        System.out.println(user3.toString());

        // c命名空间
        User user4 = (User)context.getBean("user4");
        System.out.println(user4.toString());

        User user5 = (User)context.getBean("user5");
        System.out.println(user5.toString());

    }
}
```

## 2.配置bean

### 1.构造函数配置bean

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--构造函数-->
    <bean id="user2" class="com.hello.demo.spring.bean.xml.common.User">
        <constructor-arg name="name" value="user2"/>
        <constructor-arg name="age" value="11"/>
    </bean>

</beans>
```

### 2.setter配置bean

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--setter-->
    <bean id="user1" class="com.hello.demo.spring.bean.xml.common.User">
        <property name="name" value="user1"/>
        <property name="age" value="18"/>
    </bean>

</beans>
```

### 3.p命名空间配置bean

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <!--p命名空间-->
    <bean id="user3" class="com.hello.demo.spring.bean.xml.common.User" p:age="19" p:name="user3">
    </bean>

</beans>
```

### 4.c命名空间配置bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:c="http://www.springframework.org/schema/c"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">


    <!--c命名空间-->
    <bean id="user4" class="com.hello.demo.spring.bean.xml.common.User" c:_0="user4" c:_1="20">
    </bean>

    <!--c命名空间-->
    <bean id="user5" class="com.hello.demo.spring.bean.xml.common.User" c:name="user5" c:age="21">
    </bean>

</beans>
```

**注意**

当使用c:key 配置bean时，抛出下面异常

```text
Exception in thread "main" org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'user5' defined in class path resource [bean.xml]: Unsatisfied dependency expressed through constructor parameter 1: Could not convert argument value of type [java.lang.String] to required type [int]: Failed to convert value of type 'java.lang.String' to required type 'int'; For input string: "user5"
	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:766)
	at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:240)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1381)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1218)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:563)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:523)
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339)
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:346)
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.instantiateSingleton(DefaultListableBeanFactory.java:1155)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingleton(DefaultListableBeanFactory.java:1121)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:1056)
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:987)
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627)
	at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:144)
	at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:85)
	at com.hello.demo.spring.bean.xml.BeanDemo.main(BeanDemo.java:9)
```

**解决方案**

[链接](https://github.com/spring-projects/spring-framework/issues/32188)

加上`-parameters`参数

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                    <parameters>true</parameters>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

### 5.特殊值

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <!--特殊值-->
    <bean id="user6" class="com.hello.demo.spring.bean.xml.common.User">
        <property name="name">
            <null/>
        </property>
        <property name="age" value="10"/>
    </bean>

    <!--转义-->
    <bean id="user7" class="com.hello.demo.spring.bean.xml.common.User">
        <property name="name" value="a &lt; b"/>
        <property name="age" value="10"/>
    </bean>

    <!--CDATA-->
    <bean id="user8" class="com.hello.demo.spring.bean.xml.common.User">
        <property name="name">
            <value><![CDATA[a < b]]></value>
        </property>
        <property name="age" value="10"/>
    </bean>
    
</beans>
```

### 6.对象引用

`bean.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <!--对象-->
    <bean id="book1" class="com.hello.demo.spring.bean.xml.common.Book">
        <property name="bookName" value="book1"/>
        <property name="price" value="10"/>
    </bean>

    <!--外部bean引用-->
    <bean id="user9" class="com.hello.demo.spring.bean.xml.common.User1">
        <property name="name" value="user9"/>
        <property name="age" value="18"/>
        <property name="book" ref="book1"/>
    </bean>

    <!--内部bean-->
    <bean id="user10" class="com.hello.demo.spring.bean.xml.common.User1">
        <property name="name" value="user9"/>
        <property name="age" value="18"/>

        <property name="book">
            <bean class="com.hello.demo.spring.bean.xml.common.Book">
                <property name="bookName" value="book"/>
                <property name="price" value="10"/>
            </bean>
        </property>
    </bean>

    <!--级联属性赋值-->
    <!--Book属性要用getter-->
    <bean id="book2" class="com.hello.demo.spring.bean.xml.common.Book">
        <property name="bookName" value="book2"/>
        <property name="price" value="10"/>
    </bean>
    <bean id="user11" class="com.hello.demo.spring.bean.xml.common.User1">
        <property name="name" value="user9"/>
        <property name="age" value="18"/>
        <property name="book" ref="book2"/>
        <property name="book.bookName" value="book"/>
        <property name="book.price" value="10"/>
    </bean>

</beans>
```

### 7.数组

`bean.xml`

```xml
<property name="bookArray">
    <array>
        <bean class="com.hello.demo.spring.bean.xml.common.Book">
            <property name="bookName" value="book"/>
            <property name="price" value="10"/>
        </bean>
        <bean class="com.hello.demo.spring.bean.xml.common.Book">
            <property name="bookName" value="book"/>
            <property name="price" value="10"/>
        </bean>
    </array>
</property>
```

### 8.集合

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="book1" class="com.hello.demo.spring.bean.xml.common.Book">
        <property name="bookName" value="book1"/>
        <property name="price" value="10"/>
    </bean>
    
    <bean id="book2" class="com.hello.demo.spring.bean.xml.common.Book">
        <property name="bookName" value="book2"/>
        <property name="price" value="10"/>
    </bean>
    
    <bean id="user12" class="com.hello.demo.spring.bean.xml.common.User2">
        <!--Array-->
        <property name="bookArray">
            <array>
                <bean class="com.hello.demo.spring.bean.xml.common.Book">
                    <property name="bookName" value="book"/>
                    <property name="price" value="10"/>
                </bean>
                <bean class="com.hello.demo.spring.bean.xml.common.Book">
                    <property name="bookName" value="book"/>
                    <property name="price" value="10"/>
                </bean>
            </array>
        </property>

        <!--List-->
        <property name="bookList">
            <list>
                <ref bean="book1"/>
                <ref bean="book1"/>
                <ref bean="book1"/>
            </list>
        </property>

        <!--Set-->
        <property name="bookSet">
            <set>
                <ref bean="book1"/>
                <ref bean="book2"/>
            </set>
        </property>

        <!--Map-->
        <property name="bookMap">
            <map>
                <entry key="book1" value-ref="book1"/>
                <entry key="book2" value-ref="book2"/>
            </map>
        </property>

        <property name="bookProperties">
            <props>
                <prop key="bookName">book</prop>
                <prop key="price">10</prop>
            </props>
        </property>
    </bean>
    
</beans>
```

### 9.util命名空间

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/util
       https://www.springframework.org/schema/util/spring-util.xsd">
    
    <bean id="book1" class="com.hello.demo.spring.bean.xml.common.Book">
        <property name="bookName" value="book1"/>
        <property name="price" value="10"/>
    </bean>
    
    <bean id="book2" class="com.hello.demo.spring.bean.xml.common.Book">
        <property name="bookName" value="book2"/>
        <property name="price" value="10"/>
    </bean>

    <!--util命名空间-->
    <bean id="user13" class="com.hello.demo.spring.bean.xml.common.User2">
        <property name="bookArray">
            <array>
                <bean class="com.hello.demo.spring.bean.xml.common.Book">
                    <property name="bookName" value="book"/>
                    <property name="price" value="10"/>
                </bean>
                <bean class="com.hello.demo.spring.bean.xml.common.Book">
                    <property name="bookName" value="book"/>
                    <property name="price" value="10"/>
                </bean>
            </array>
        </property>

        <property name="bookList">
            <util:list>
                <ref bean="book1"/>
                <ref bean="book1"/>
                <ref bean="book1"/>
            </util:list>
        </property>

        <property name="bookMap">
            <util:map>
                <entry key="book1" value-ref="book1"/>
                <entry key="book2" value-ref="book2"/>
            </util:map>
        </property>

        <property name="bookSet">
            <util:set>
                <ref bean="book1"/>
                <ref bean="book2"/>
            </util:set>
        </property>

        <property name="bookProperties">
            <util:properties>
                <prop key="bookName">book</prop>
                <prop key="price">10</prop>
            </util:properties>
        </property>
    </bean>

</beans>
```

### 10.引入外部属性文件

`user.properties`
```properties
user.name='hello你好123'
user.age=12
```

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"

       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/util
       https://www.springframework.org/schema/util/spring-util.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    
    
    <!--引入外部属性文件-->
    <!--user.properties编码为utf-8-->
    <bean id="propertyConfigurer"
      class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="location" value="classpath:user.properties"/>
        <property name="fileEncoding" value="UTF-8"/>
    </bean>

    <context:property-placeholder location="classpath:user.properties"/>



    <bean id="user14" class="com.hello.demo.spring.bean.xml.common.User">
        <property name="age" value="${user.age}"/>
        <property name="name" value="${user.name}"/>
    </bean>

</beans>
```

### 11.bean作用域

[bean作用域](./Spring-Bean作用域.md)

```xml

<bean id="user1" class="com.hello.demo.spring.bean.xml.common.User" scope="prototype">
    <property name="name" value="user1"/>
    <property name="age" value="18"/>
</bean>
```

### 12.自动装配

`bean.xml`
```xml
<beans>
    <bean id="userService" class="com.hello.demo.spring.bean.xml.common.UserService" autowire="byType"/>
    <bean id="userDaoImpl1-1" class="com.hello.demo.spring.bean.xml.common.UserDaoImpl1"/>
    
    userService 有个userDao 属性
    <bean id="userService" class="com.hello.demo.spring.bean.xml.common.UserService" autowire="byName"/>
    <bean id="userDao" class="com.hello.demo.spring.bean.xml.common.UserDaoImpl1"/>
</beans>

```

### 13.开启组件扫描

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
    <!--开启组件扫描功能-->
    <context:component-scan base-package="com.xxxx"/>
</beans>
```
