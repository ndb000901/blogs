# Spring-xml配置常用命名空间


## 1. **`http://www.springframework.org/schema/beans`**  
   这是Spring的核心命名空间，用于定义基本的Bean配置。常用于定义`<bean>`元素，设置Bean的属性、构造函数参数、生命周期等。

   示例：
   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd">
       <bean id="myBean" class="com.example.MyBean"/>
   </beans>
   ```

## 2. **`http://www.springframework.org/schema/context`**  
   用于启用Spring的上下文支持，允许自动扫描指定包中的Bean，并支持配置注解驱动的功能（如`@Autowired`、`@PostConstruct`等）。

   示例：
   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/context
              http://www.springframework.org/schema/context/spring-context.xsd">
       <context:component-scan base-package="com.example"/>
   </beans>
   ```

## 3. **`http://www.springframework.org/schema/aop`**  
   用于配置Spring AOP（面向切面编程）相关的功能。可以配置切面、通知等。

   示例：
   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/aop
              http://www.springframework.org/schema/aop/spring-aop.xsd">
       <aop:config>
           <aop:aspect ref="myAspect">
               <aop:pointcut id="myPointcut" expression="execution(* com.example.service.*.*(..))"/>
               <aop:around pointcut-ref="myPointcut" method="logAround"/>
           </aop:aspect>
       </aop:config>
   </beans>
   ```

## 4. **`http://www.springframework.org/schema/tx`**  
   用于配置事务管理。可以配置声明式事务，定义事务的传播行为、隔离级别等。

   示例：
   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/tx
              http://www.springframework.org/schema/tx/spring-tx.xsd">
       <tx:advice id="txAdvice" transaction-manager="txManager">
           <tx:attributes>
               <tx:method name="*" propagation="REQUIRED"/>
           </tx:attributes>
       </tx:advice>
   </beans>
   ```

## 5. **`http://www.springframework.org/schema/security`**  
   用于配置Spring Security的相关设置，配置安全控制、认证和授权等。

   示例：
   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:security="http://www.springframework.org/schema/security"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/security
              http://www.springframework.org/schema/security/spring-security.xsd">
       <security:http>
           <security:intercept-url pattern="/admin/**" access="ROLE_ADMIN"/>
           <security:form-login login-page="/login"/>
       </security:http>
   </beans>
   ```

## 6. **`http://www.springframework.org/schema/orm`**  
   用于配置与ORM（Object-Relational Mapping）相关的功能，例如JPA或Hibernate的配置。

   示例：
   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:orm="http://www.springframework.org/schema/orm"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/orm
              http://www.springframework.org/schema/orm/spring-orm.xsd">
       <orm:jpa-entity-manager-factory id="entityManagerFactory" 
                                       persistence-unit-name="myJpaUnit"/>
   </beans>
   ```

## 7. **`http://www.springframework.org/schema/cache`**  
   用于配置缓存支持，通常与Spring的缓存抽象一起使用，支持不同的缓存实现。

   示例：
   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:cache="http://www.springframework.org/schema/cache"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/cache
              http://www.springframework.org/schema/cache/spring-cache.xsd">
       <cache:annotation-driven/>
   </beans>
   ```
   

## **8. `p` 命名空间（简化 `<property>` 配置）**
**作用**：让 `<property>` 赋值更加简洁。  
**声明**：
```xml
xmlns:p="http://www.springframework.org/schema/p"
```
**示例**：
```xml
<bean id="user" class="com.example.User" p:name="Alice" p:age="25"/>
```
**等价于**：
```xml
<bean id="user" class="com.example.User">
    <property name="name" value="Alice"/>
    <property name="age" value="25"/>
</bean>
```

---

## **9. `c` 命名空间（简化 `<constructor-arg>` 配置）**
**作用**：简化 **构造函数** 注入的 XML 配置。  
**声明**：
```xml
xmlns:c="http://www.springframework.org/schema/c"
```
**示例**：
```xml
<bean id="user" class="com.example.User" c:name="Alice" c:age="25"/>
```
**等价于**：
```xml
<bean id="user" class="com.example.User">
    <constructor-arg name="name" value="Alice"/>
    <constructor-arg name="age" value="25"/>
</bean>
```
如果构造函数没有 **参数名**，可以使用 **索引**：
```xml
<bean id="user" class="com.example.User" c:_0="Alice" c:_1="25"/>
```

---


## **10. `jdbc` 命名空间（JDBC 相关配置）**
**作用**：用于 **数据源、JDBC 操作模板** 配置。  
**声明**：
```xml
xmlns:jdbc="http://www.springframework.org/schema/jdbc"
```
**示例**：
```xml
<jdbc:embedded-database id="dataSource">
    <jdbc:script location="classpath:schema.sql"/>
    <jdbc:script location="classpath:data.sql"/>
</jdbc:embedded-database>
```

---

## **11. `jee` 命名空间（JNDI 资源绑定）**
**作用**：用于在 Java EE 服务器上 **绑定 JNDI 资源**。  
**声明**：
```xml
xmlns:jee="http://www.springframework.org/schema/jee"
```
**示例**：
```xml
<jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/MyDS"/>
```

---

## **12. `mvc` 命名空间（Spring MVC 相关配置）**
**作用**：简化 **Spring MVC** 配置，比如 **启用 `@Controller`**、静态资源映射等。  
**声明**：
```xml
xmlns:mvc="http://www.springframework.org/schema/mvc"
```
**示例**：
```xml
<mvc:annotation-driven/>
<mvc:resources mapping="/static/**" location="/public/"/>
```

---

## **13. `util` 命名空间（简化集合类型配置）**
**作用**：用于配置 **List、Set、Map、Properties** 等集合类型。  
**声明**：
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
**示例**：
```xml
<util:list id="myList">
    <value>Item1</value>
    <value>Item2</value>
</util:list>

<util:map id="myMap">
    <entry key="key1" value="value1"/>
    <entry key="key2" value="value2"/>
</util:map>
```

---

## **14. `task` 命名空间（异步任务调度）**
**作用**：用于 **线程池** 和 **定时任务**。  
**声明**：
```xml
xmlns:task="http://www.springframework.org/schema/task"
```
**示例**：
```xml
<task:executor id="myExecutor" pool-size="5"/>
<task:scheduler id="myScheduler" pool-size="10"/>
<task:scheduled-tasks>
    <task:scheduled ref="myBean" method="runTask" fixed-rate="5000"/>
</task:scheduled-tasks>
```


## **15. `lang` 命名空间（动态语言支持，如 Groovy、JS）**
**作用**：用于在 Spring 中 **动态加载 Groovy、JavaScript、Ruby 代码**。  
**声明**：
```xml
xmlns:lang="http://www.springframework.org/schema/lang"
```
**示例**：
```xml
<lang:groovy id="myScript" script-source="classpath:myscript.groovy"/>
```
