# Spring-注解配置Bean

## 1.依赖
```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.2.3</version>
</dependency>
```

## 2.获取容器上下文

`AnnotationDemo.java`
```java
public class AnnotationDemo {
    public static void main(String[] args) {

        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AnnotationConfig.class);
        UserController userController = (UserController) context.getBean(UserController.class);
        System.out.println(userController.getUSer());

        Book book = (Book) context.getBean(Book.class);
        System.out.println(book);
    }
}
```

## 3.配置组件扫描

`AnnotationConfig.java`
```java
package com.hello.demo.spring.bean.annotation.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.hello")
public class AnnotationConfig {
}

```

## 4.常用注解

Spring 框架提供了大量的注解，涵盖 **组件管理、依赖注入、AOP、事务管理、Web 开发** 等方面。

---

## **1. 组件管理（Spring Bean 相关）**
### **1.1 @Component（通用组件）**
- **作用**：将类标记为 Spring 容器中的一个组件（Bean）。
- **等价于 XML 配置**：`<bean id="xxx" class="xxx"/>`
- **示例**：
  ```java

    @Component
    public class Book {
    
        private String name;
    
        private int price;
    
        @Override
        public String toString() {
            return "Book{" +
                    "name='" + name + '\'' +
                    ", price=" + price +
                    '}';
        }
    }

  ```

---

### **1.2 @Service（服务层组件）**
- **作用**：标记 **业务逻辑层（Service）** 组件，实际上等价于 `@Component`，只是语义更明确。
- **示例**：
  ```java

    @Service
    public class UserServiceImpl implements UserService {
    
        @Autowired
        private UserDao userDao;
    
        @Override
        public User getUser() {
            System.out.println("UserServiceImpl.getUser run...");
            return this.userDao.getUser();
        }
    }

  ```

---

### **1.3 @Repository（数据访问层组件）**
- **作用**：标记 **DAO（数据访问层）** 组件，等价于 `@Component`，Spring 可能会为其提供 **异常转换** 处理。
- **示例**：
  ```java

    @Repository
    public class UserDaoImpl implements UserDao {
    
        @Override
        public User getUser() {
            System.out.println("UserDaoImpl.getUser run...");
            return new User();
        }
    }
  ```

---

### **1.4 @Controller（控制层组件）**
- **作用**：标记 **控制层（Controller）** 组件，主要用于 Spring MVC 处理 HTTP 请求。
- **示例**：
  ```java
    @Controller
    public class UserController {
    
        @Autowired
        private UserService userService;

        public User getUSer() {
            System.out.println("UserController.getUser run...");
            return this.userService.getUser();
        }
    }
  ```

---

## **2. 依赖注入（DI）相关**
### **2.1 @Autowired（自动注入）**
- **作用**：自动注入 Spring 容器中的 Bean，可用于 **构造方法、Setter 方法、字段**。
- **默认按照** **类型（byType）** 注入，如果存在多个同类型 Bean，可以结合 `@Qualifier` 指定 Bean 名称。
- **示例**：
  ```java

    @Controller
    public class UserController {
    
        @Autowired
        private UserService userService;
    
    
        public User getUSer() {
            System.out.println("UserController.getUser run...");
            return this.userService.getUser();
        }
    }
  ```
  
- **setter 方法注入**：
  ```java
    @Controller
    public class UserController {
        
        private UserService userService;
    
        @Autowired
        public void setUserService(UserService userService) {
            this.userService = userService;
        }
    
        public User getUSer() {
            System.out.println("UserController.getUser run...");
            return this.userService.getUser();
        }
    }
  ```

- **构造方法注入**：
  ```java
    @Controller
    public class UserController {
    
        private UserService userService;
    
        @Autowired
        public UserController(UserService userService) {
            this.userService = userService;
        }
    
        
        public User getUSer() {
            System.out.println("UserController.getUser run...");
            return this.userService.getUser();
        }
    }
  ```

---

### **2.2 @Qualifier（指定 Bean 名称）**
- **作用**：用于配合 `@Autowired` 解决 **多个相同类型 Bean 时的歧义**。
- **示例**：
  ```java
    @Controller
    public class UserController {
    
        @Autowired
        @Qualifier("userServiceImpl") //实现类的类名称小驼峰
        private UserService userService;
    
        
        public User getUSer() {
            System.out.println("UserController.getUser run...");
            return this.userService.getUser();
        }
    }
  ```

---

### **2.3 @Primary（优先注入）**
- **作用**：如果多个 Bean 满足 `@Autowired`，`@Primary` 的 Bean **优先注入**。
- **示例**：
  ```java
    @Service
    public class UserServiceImpl implements UserService {
    
        @Autowired
        private UserDao userDao;
    
        @Override
        public User getUser() {
            System.out.println("UserServiceImpl.getUser run...");
            return this.userDao.getUser();
        }
    }

    // 优先注入
    @Service
    @Primary
    public class UserServiceImpl2 implements UserService {
    
        @Autowired
        private UserDao userDao;
    
        @Override
        public User getUser() {
            System.out.println("UserServiceImpl2.getUser run...");
            return this.userDao.getUser();
        }
    }
  ```

---

### **2.4 @Resource（JSR-250 注解，按名称注入）**
- **作用**：类似 `@Autowired`，但默认按照 **Bean 名称** 注入，而不是类型。
- **示例**：
  ```java
    @Controller
    public class UserController {
    
        // 不写name 默认是类型注入
        @Resource(name = "userServiceImpl")
        private UserService userService;
    
    
        public User getUSer() {
            System.out.println("UserController.getUser run...");
            return this.userService.getUser();
        }
    }
  ```
- 需要依赖
  ```xml
  <!-- https://mvnrepository.com/artifact/jakarta.annotation/jakarta.annotation-api -->
  <dependency>
      <groupId>jakarta.annotation</groupId>
      <artifactId>jakarta.annotation-api</artifactId>
      <version>3.0.0</version>
  </dependency>
---

## **3. 配置相关**
### **3.1 @Configuration（配置类）**
- **作用**：标记一个类是 **Spring 配置类**，可以用 `@Bean` 定义 Bean。
- **示例**：
  ```java

    import org.springframework.context.annotation.ComponentScan;
    import org.springframework.context.annotation.Configuration;
    
    @Configuration
    @ComponentScan("com.hello")
    public class AnnotationConfig {
    }

  ```

---

### **3.2 @Bean（定义 Bean）**
- **作用**：在 `@Configuration` 类中定义 Bean，相当于 XML 中的 `<bean>`。
- **示例**：
  ```java
    @Configuration
    public class AppConfig {
    
        @Bean
        public UserController getUserController() {
            return new UserController();
        }
    }
  ```

---

## **4. AOP（面向切面编程）相关**
### **4.1 @Aspect（定义切面）**
- **作用**：声明一个类是 **切面（Aspect）**，用于 AOP 。
- **示例**：
  ```java
  @Aspect
  @Component
  public class LoggingAspect {
      @Before("execution(* com.example.service.*.*(..))")
      public void beforeMethod() {
          System.out.println("Before method execution");
      }
  }
  ```

---

### **4.2 @Before（前置通知）**
- **作用**：在 **方法执行前** 运行。
- **示例**：
  ```java
  @Before("execution(* com.example.service.*.*(..))")
  public void logBefore() {
      System.out.println("Method execution started...");
  }
  ```

---

### **4.3 @After（后置通知）**
- **作用**：在 **方法执行后** 运行。
- **示例**：
  ```java
  @After("execution(* com.example.service.*.*(..))")
  public void logAfter() {
      System.out.println("Method execution finished...");
  }
  ```

---

## **5. 事务管理**
### **5.1 @Transactional（事务管理）**
- **作用**：开启数据库事务，确保操作的原子性。
- **示例**：
  ```java
  @Service
  public class UserService {
      @Transactional
      public void registerUser() {
          // 事务操作
      }
  }
  ```

---

## **6. Web 相关**
### **6.1 @RequestMapping（映射请求）**
- **作用**：用于 **控制器方法**，映射 HTTP 请求到方法。
- **示例**：
  ```java
  @Controller
  public class UserController {
      @RequestMapping("/hello")
      public String sayHello() {
          return "Hello, Spring!";
      }
  }
  ```

---

### **6.2 @RestController（Rest 风格 Controller）**
- **作用**：`@Controller` + `@ResponseBody`，用于 RESTful API。
- **示例**：
  ```java
  @RestController
  public class UserController {
      @GetMapping("/user")
      public User getUser() {
          return new User("John");
      }
  }
  ```









