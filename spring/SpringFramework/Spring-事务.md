# Spring-事务

## 1.事务的 4 大特性(ACID)
| 特性 | 说明 |
|------|------|
| **原子性（Atomicity）** | 事务中的所有操作要么全部成功，要么全部失败 |
| **一致性（Consistency）** | 事务执行后，数据库仍保持一致性 |
| **隔离性（Isolation）** | 多个事务并发执行时，相互之间不受影响 |
| **持久性（Durability）** | 事务一旦提交，修改的数据必须永久保存 |

---

## 2. 事务的 5 种隔离级别
| 级别 | 能否发生脏读 | 能否发生不可重复读 | 能否发生幻读 |
|------|------------|----------------|------------|
| **READ UNCOMMITTED** | ✅ 可能 | ✅ 可能 | ✅ 可能 |
| **READ COMMITTED** | ❌ 不可能 | ✅ 可能 | ✅ 可能 |
| **REPEATABLE READ**（MySQL 默认） | ❌ 不可能 | ❌ 不可能 | ❌（MySQL MVCC 避免幻读） |
| **SERIALIZABLE** | ❌ 不可能 | ❌ 不可能 | ❌ 不可能 |

> Spring 事务默认隔离级别为 **`DEFAULT`**，会使用 **数据库默认隔离级别**（如 MySQL 默认是 `REPEATABLE READ`）。

---

## **3. 事务的 7 种传播机制**
| 传播行为 | 说明 |
|------|------|
| **`REQUIRED`（默认）** | 如果当前没有事务，则新建事务；如果有，则加入当前事务 |
| **`REQUIRES_NEW`** | **始终新建事务**，如果已有事务，先挂起原事务 |
| **`SUPPORTS`** | 如果有事务，就加入；如果没有，不开启事务 |
| **`NOT_SUPPORTED`** | 以**非事务**方式运行，若当前有事务，则挂起事务 |
| **`MANDATORY`** | 必须在**已有事务**的情况下运行，否则抛异常 |
| **`NEVER`** | 必须**无事务**运行，否则抛异常 |
| **`NESTED`** | 在**当前事务中开启子事务**，子事务可回滚，不影响主事务 |

> **常见用法**
> - `REQUIRED`：适用于大部分情况
> - `REQUIRES_NEW`：适用于**子事务需独立提交或回滚**
> - `NESTED`：适用于**子事务回滚不影响主事务**

### 示例
```java
@Transactional(
    propagation = Propagation.REQUIRES_NEW,   // 总是新建事务
    isolation = Isolation.READ_COMMITTED,    // 读已提交
    rollbackFor = Exception.class,          // 遇到 Exception 及其子类回滚
    timeout = 5                              // 5 秒超时
)
public void processOrder() {
    // 业务逻辑
}
```

### 可配置的属性

| 配置 | 说明 |
|------|------|
| `propagation` | 事务传播行为 |
| `isolation` | 事务隔离级别 |
| `rollbackFor` | 指定回滚的异常 |
| `noRollbackFor` | 指定不回滚的异常 |
| `timeout` | 事务超时时间（秒） |
| `readOnly` | 是否为只读事务（提升查询性能） |

## 4.源码(xml)

[Spring-事务基于xml](./Spring-事务基于xml.md)

## 5.源码(注解)

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
|   |   |                       `-- annotation
|   |   |                           |-- common
|   |   |                           |   `-- User.java
|   |   |                           |-- config
|   |   |                           |   `-- AppConfig.java
|   |   |                           |-- controllers
|   |   |                           |   `-- UserController.java
|   |   |                           |-- dao
|   |   |                           |   |-- UserDaoImpl.java
|   |   |                           |   `-- UserDao.java
|   |   |                           |-- service
|   |   |                           |   |-- UserServiceImpl.java
|   |   |                           |   `-- UserService.java
|   |   |                           `-- TxAnnotationDemo.java
|   |   `-- resources
|   |       |-- jdbc.properties
|   |       `-- log4j2.xml

```

`User.java`
```java
package com.hello.demo.spring.tx.annotation.common;

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
package com.hello.demo.spring.tx.annotation.controllers;

import com.hello.demo.spring.tx.annotation.common.User;
import com.hello.demo.spring.tx.annotation.service.UserService;
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
package com.hello.demo.spring.tx.annotation.service;


import com.hello.demo.spring.tx.annotation.common.User;

public interface UserService {

    public User getUser(long id);

    public void addUser(User user);

    public void updateUser(User user);

    public void deleteUser(long id);

}

```

`UserServiceImpl.java`
```java
package com.hello.demo.spring.tx.annotation.service;

import com.hello.demo.spring.tx.annotation.common.User;
import com.hello.demo.spring.tx.annotation.dao.UserDao;
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
package com.hello.demo.spring.tx.annotation.dao;


import com.hello.demo.spring.tx.annotation.common.User;

public interface UserDao {

    User getUser(long id);

    void addUser(User user);

    void deleteUser(long id);

    void updateUser(User user);
}

```

`UserDaoImpl.java`
```java
package com.hello.demo.spring.tx.annotation.dao;

import com.hello.demo.spring.tx.annotation.common.User;
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

    @Transactional
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

`AppConfig.java`
```java
package com.hello.demo.spring.tx.annotation.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;

@Configuration
@ComponentScan("com.hello")
@PropertySource("classpath:jdbc.properties")
@EnableTransactionManagement
public class AppConfig {

    @Value("${jdbc.user}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Value("${jdbc.driver}")
    private String driverClassName;

    @Value("${jdbc.url}")
    private String url;

    @Bean
    public DruidDataSource druidDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUsername(this.username);
        druidDataSource.setPassword(this.password);
        druidDataSource.setDriverClassName(this.driverClassName);
        druidDataSource.setUrl(this.url);
        return druidDataSource;
    }

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

    @Bean
    public DataSourceTransactionManager dataSourceTransactionManager(DataSource dataSource) {
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(dataSource);
        return dataSourceTransactionManager;
    }

}

```

`TxAnnotationDemo.java`
```java
package com.hello.demo.spring.tx.annotation;

import com.hello.demo.spring.tx.annotation.common.User;
import com.hello.demo.spring.tx.annotation.config.AppConfig;
import com.hello.demo.spring.tx.annotation.controllers.UserController;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class TxAnnotationDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

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





