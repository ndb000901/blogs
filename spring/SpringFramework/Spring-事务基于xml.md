# Spring-事务基于xml

## 源码

目录结构
```text
|-- pom.xml
|-- src
|   |-- main
|   |   |-- java
|   |   |   `-- com
|   |   |       `-- hello
|   |   |           `-- demo
|   |   |               `-- spring
|   |   |                   `-- tx
|   |   |                       `-- xml
|   |   |                           |-- common
|   |   |                           |   `-- User.java
|   |   |                           |-- controllers
|   |   |                           |   `-- UserController.java
|   |   |                           |-- dao
|   |   |                           |   |-- UserDaoImpl.java
|   |   |                           |   `-- UserDao.java
|   |   |                           |-- service
|   |   |                           |   |-- UserServiceImpl.java
|   |   |                           |   `-- UserService.java
|   |   |                           `-- TxXmlDemo.java
|   |   `-- resources
|   |       |-- bean.xml
|   |       |-- jdbc.properties
|   |       `-- log4j2.xml

```

`User.java`
```java
package com.hello.demo.spring.tx.xml.common;

public class User {

    private long id;

    private String name;

    private int age;

    private String email;

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", email='" + email + '\'' +
                '}';
    }
}


```

`UserController.java`
```java
package com.hello.demo.spring.tx.xml.controllers;

import com.hello.demo.spring.tx.xml.common.User;
import com.hello.demo.spring.tx.xml.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

@Controller
public class UserController {

    @Autowired
    private UserService userService;

    public User getUser(long id) {
        return this.userService.getUser(id);
    }

    public void addUser(User user) {
        this.userService.addUser(user);
    }

    public void updateUser(User user) {
        this.userService.updateUser(user);
    }

    public void deleteUser(long id) {
        this.userService.deleteUser(id);
    }
}

```

`UserService.java`
```java
package com.hello.demo.spring.tx.xml.service;


import com.hello.demo.spring.tx.xml.common.User;

public interface UserService {

    public User getUser(long id);

    public void addUser(User user);

    public void updateUser(User user);

    public void deleteUser(long id);

}

```

`UserServiceImpl.java`
```java
package com.hello.demo.spring.tx.xml.service;

import com.hello.demo.spring.tx.xml.common.User;
import com.hello.demo.spring.tx.xml.dao.UserDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserDao userDao;

    @Override
    public User getUser(long id) {
        return this.userDao.getUser(id);
    }

    @Override
    public void addUser(User user) {
        this.userDao.addUser(user);
    }

    @Override
    public void updateUser(User user) {
        this.userDao.updateUser(user);
    }

    @Override
    public void deleteUser(long id) {
        this.userDao.deleteUser(id);
    }
}


```

`UserDao.java`
```java
package com.hello.demo.spring.tx.xml.dao;


import com.hello.demo.spring.tx.xml.common.User;

public interface UserDao {

    User getUser(long id);

    void addUser(User user);

    void deleteUser(long id);

    void updateUser(User user);
}

```

`UserDaoImpl.java`
```java
package com.hello.demo.spring.tx.xml.dao;

import com.hello.demo.spring.tx.xml.common.User;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;

@Repository
public class UserDaoImpl implements UserDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    private static final Logger logger = LogManager.getLogger(UserDaoImpl.class);

    @Override
    public User getUser(long id) {
        String sql = "select * from user where id = ?";
        User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class),id);
        logger.info(user);
        return user;
    }

    @Override
    public void addUser(User user) {
        String sql = "insert into user(id, name, age, email) values(?, ?, ?, ?)";

        int result = this.jdbcTemplate.update(sql, user.getId(), user.getName(), user.getAge(), user.getEmail());

        logger.info(user);
    }

//    @Transactional
    @Override
    public void deleteUser(long id) {
        User user = this.getUser(id);
        String sql = "delete from user where id = ?";
        int result = this.jdbcTemplate.update(sql, id);
        String insertSql = "insert into user_bak(id, name, age, email) values(?, ?, ?, ?)";
        int insertResult = this.jdbcTemplate.update(insertSql, user.getId(), user.getName(), user.getAge(), user.getEmail());
        logger.info(result + String.valueOf(insertResult));
    }

    @Override
    public void updateUser(User user) {
        String sql = "update user set name = ?, age = ?, email = ? where id = ?";
        int result = this.jdbcTemplate.update(sql, user.getName(), user.getAge(), user.getEmail(), user.getId());

        logger.info(user);
    }
}

```

`TxXmlDemo.java`
```java
package com.hello.demo.spring.tx.xml;

import com.hello.demo.spring.tx.xml.common.User;
import com.hello.demo.spring.tx.xml.controllers.UserController;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TxXmlDemo {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("bean.xml");
        UserController userController = context.getBean(UserController.class);

        User user = new User();
        user.setAge(101);
        user.setEmail("user-tx@gmail.com");
        user.setId(101);
        user.setName("user-tx");

        // user插入一条数据
//        userController.addUser(user);
        // 删除user表记录，将删除记录写入到user_bak表中
        userController.deleteUser(user.getId());
    }
}
```

`bean.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="com.hello"/>
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="username" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
    </bean>

    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <tx:annotation-driven transaction-manager="transactionManager"/>

    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.hello.demo.spring.tx.xml.dao.UserDaoImpl.*(..))"/>
    </aop:config>

    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!-- tx:method标签：配置具体的事务方法 -->
            <!-- name属性：指定方法名，可以使用星号代表多个字符 -->
            <tx:method name="get*" read-only="true"/>

            <!-- read-only属性：设置只读属性 -->
            <!-- rollback-for属性：设置回滚的异常 -->
            <!-- no-rollback-for属性：设置不回滚的异常 -->
            <!-- isolation属性：设置事务的隔离级别 -->
            <!-- timeout属性：设置事务的超时属性 -->
            <!-- propagation属性：设置事务的传播行为 -->
            <tx:method name="add*" read-only="false" rollback-for="java.lang.Exception" propagation="REQUIRES_NEW"/>
            <tx:method name="update*" read-only="false" rollback-for="java.lang.Exception" propagation="REQUIRES_NEW"/>
            <tx:method name="delete*" read-only="false" rollback-for="java.lang.Exception" propagation="REQUIRES_NEW"/>
        </tx:attributes>
    </tx:advice>

</beans>
```

`jdbc.properties`
```properties
jdbc.user=root
jdbc.password=root
jdbc.url=jdbc:mysql://localhost:3306/spring-demo?characterEncoding=utf8&useSSL=false
jdbc.driver=com.mysql.cj.jdbc.Driver
```

`log4j2.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN"
               xmlns="https://logging.apache.org/xml/ns"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="
                   https://logging.apache.org/xml/ns
                   https://logging.apache.org/xml/ns/log4j-config-2.xsd">
    <Appenders>
        <!-- 控制台输出 -->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n" />
        </Console>

        <!-- 文件输出 -->
        <RollingFile name="FileAppender" fileName="logs/app.log"
                     filePattern="logs/app-%d{yyyy-MM-dd}.log.gz">
            <PatternLayout>
                <Pattern>%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n</Pattern>
            </PatternLayout>
            <Policies>
                <TimeBasedTriggeringPolicy />
            </Policies>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- Spring 相关日志 -->
        <Logger name="org.springframework" level="DEBUG" additivity="false">
            <AppenderRef ref="Console"/>
<!--            <AppenderRef ref="FileAppender"/>-->
        </Logger>

        <!-- 全局日志 -->
        <Root level="INFO">
            <AppenderRef ref="Console"/>
<!--            <AppenderRef ref="FileAppender"/>-->
        </Root>
    </Loggers>
</Configuration>
```


