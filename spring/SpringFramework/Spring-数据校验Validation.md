# Spring-数据校验Validation


# 1. 引入 Spring Validation 依赖
Spring 本身不包含 `Validation` 组件，因此需要引入 **Hibernate Validator** 作为 `JSR-303` / `JSR-380` 规范的实现：
```xml
<dependencies>
    <dependency>
        <groupId>org.hibernate.validator</groupId>
        <artifactId>hibernate-validator</artifactId>
        <version>7.0.5.Final</version>
    </dependency>

    <dependency>
        <groupId>org.glassfish</groupId>
        <artifactId>jakarta.el</artifactId>
        <version>4.0.2</version>
    </dependency>
</dependencies>
```
✅ **注意**：`jakarta.el` 依赖是为了支持 EL 表达式，否则可能会出现 `javax.el.ExpressionFactory` 相关异常。

---

# 2. 常见校验注解
Spring Validation 主要通过 **JSR-303/JSR-380 规范的注解** 进行字段校验。

| **注解**                 | **作用**                                      |
|--------------------------|-----------------------------------------------|
| `@NotNull`              | 字段不能为空（但可以是空字符串 `""`）         |
| `@NotEmpty`             | 不能为空（适用于字符串、集合、数组）          |
| `@NotBlank`             | 不能为空且必须包含非空白字符（适用于字符串）  |
| `@Size(min=, max= )`    | 限制字符长度或集合大小                        |
| `@Min(value)`           | 限制最小值（适用于数字）                      |
| `@Max(value)`           | 限制最大值（适用于数字）                      |
| `@Pattern(regexp= )`    | 必须匹配正则表达式                            |
| `@Email`                | 必须是有效的电子邮件格式                      |
| `@Positive`             | 必须是正数                                    |
| `@Negative`             | 必须是负数                                    |
| `@Past`                 | 必须是过去的日期                              |
| `@Future`               | 必须是未来的日期                              |
| `@Valid`                | 级联校验（用于嵌套对象）                      |

## 3.通过`Validator`接口实现

`user.java`
```java
package com.hello.demo.spring.validator.common;

public class User {

    private String name;

    private int age;

    private String email;

    private String phoneNumber;

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

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

`UserValidator.java`
```java

package com.hello.demo.spring.validator.common;

import org.springframework.validation.Errors;
import org.springframework.validation.Validator;

public class UserValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return User.class.equals(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        User user = (User) target;
        // 0 <= age <=150
        if (user.getAge() < 0 || user.getAge() > 150) {
            errors.rejectValue("age", "user.age.invalid");
        }
        // name 长度1-64字符
        else if (user.getName().isEmpty() || user.getName().length() > 64) {
            errors.rejectValue("name", "user.name.invalid())");
        }
        // email 长度1-64
        else if (user.getEmail().isEmpty() || user.getEmail().length() > 64 || !user.getEmail().contains("@")) {
            errors.rejectValue("email", "user.email.invalid");

        }
        // phoneNumber 长度1-16
        else if (user.getPhoneNumber().isEmpty() || user.getPhoneNumber().length() > 16) {
            errors.rejectValue("phoneNumber", "user.phoneNumber.invalid");
        }
    }
}

```

`ValidatorDemo.java`
```java
package com.hello.demo.spring.validator;

import com.hello.demo.spring.validator.common.User;
import com.hello.demo.spring.validator.common.UserValidator;
import org.springframework.validation.BindingResult;
import org.springframework.validation.DataBinder;

public class ValidatorDemo {
    public static void main(String[] args) {
        User user = new User();
        user.setAge(1000);
        user.setName("hello");
        user.setEmail("hello@gmail.com");
        user.setPhoneNumber("12345678");

        DataBinder dataBinder = new DataBinder(user);
        dataBinder.addValidators(new UserValidator());

        dataBinder.validate();

        BindingResult bindingResult = dataBinder.getBindingResult();
        System.out.println(bindingResult.getAllErrors());
    }
}

```

## 4.Bean Validation注解实现

`User1.java`
```java
package com.hello.demo.spring.validator.common;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.Max;
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.Size;

public class User1 {

    @Size(min = 1, max = 64, message = "Name must be between 1 and 64 characters")
    private String name;

    @Max(value = 200, message = "Age must be less than 200")
    @Min(value = 0, message = "Age must be greater than 0")
    private int age;

    @Email
    private String email;

    @Size(min = 1, max = 16, message = "Phone number must be between 1 and 64 characters")
    private String phoneNumber;

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

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}

```

`UserService1.java`

`org.springframework.validation.Validator` 实现

```java
package com.hello.demo.spring.validator.service;

import com.hello.demo.spring.validator.common.User1;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.validation.BindException;
import org.springframework.validation.Validator;

@Service
public class User1Service1 {

    @Autowired
    private Validator validator;

    public boolean validaUser1ByValidator(User1 user) {
        BindException bindException = new BindException(user, user.getName());
        this.validator.validate(user, bindException);
        System.out.println(bindException.getAllErrors());
        return bindException.hasErrors();
    }

}

```

`UserService2.java`

`jakarta.validation.Validator` 实现

```java
package com.hello.demo.spring.validator.service;

import com.hello.demo.spring.validator.common.User1;
import jakarta.validation.ConstraintViolation;
import jakarta.validation.Validator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.Set;

@Service
public class User1Service2 {

    @Autowired
    private Validator validator;

    public boolean validatorUser1(User1 user) {
        Set<ConstraintViolation<User1>> sets = this.validator.validate(user);
        System.out.println(sets);
        return !sets.isEmpty();
    }
}


```

`AppConfig.java`
```java
package com.hello.demo.spring.validator.config;

import com.hello.demo.spring.validator.common.User1;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.validation.beanvalidation.LocalValidatorFactoryBean;
import org.springframework.validation.beanvalidation.MethodValidationPostProcessor;

@Configuration
@ComponentScan("com.hello")
public class AppConfig {

    @Bean("user1")
    public User1 getUser1() {
        User1 user1 = new User1();
        user1.setAge(1000);
        user1.setName("user1");
        user1.setEmail("user1gmail.com");
        user1.setPhoneNumber("12345678");
        return user1;
    }

    @Bean
    public LocalValidatorFactoryBean validator() {
        return new LocalValidatorFactoryBean();
    }
    
}

```

`User1ValidatorDemo.java`

```java
package com.hello.demo.spring.validator;

import com.hello.demo.spring.validator.common.User1;
import com.hello.demo.spring.validator.config.AppConfig;
import com.hello.demo.spring.validator.service.User1Service1;
import com.hello.demo.spring.validator.service.User1Service2;
import com.hello.demo.spring.validator.service.User1Service3;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class User1ValidatorDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

        User1 user1 = context.getBean("user1", User1.class);
        System.out.println(user1);

        User1Service1 user1Service1 = context.getBean(User1Service1.class);
        System.out.println("result1: " + user1Service1.validaUser1ByValidator(user1));

        User1Service2 user1Service2 = context.getBean(User1Service2.class);
        System.out.println("result2: " + user1Service2.validatorUser1((user1)));

        User1Service3 user1Service3 = context.getBean(User1Service3.class);
        user1Service3.validatorUser(user1);
    }
}
```

## 5.基于方法实现校验

`AppConfig.java`

需配置`MethodValidationPostProcessor`

```java
package com.hello.demo.spring.validator.config;

import com.hello.demo.spring.validator.common.User1;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.validation.beanvalidation.LocalValidatorFactoryBean;
import org.springframework.validation.beanvalidation.MethodValidationPostProcessor;

@Configuration
@ComponentScan("com.hello")
public class AppConfig {

    @Bean("user1")
    public User1 getUser1() {
        User1 user1 = new User1();
        user1.setAge(1000);
        user1.setName("user1");
        user1.setEmail("user1gmail.com");
        user1.setPhoneNumber("12345678");
        return user1;
    }

    @Bean
    public LocalValidatorFactoryBean validator() {
        return new LocalValidatorFactoryBean();
    }

    @Bean
    public MethodValidationPostProcessor validationPostProcessor() {
        return new MethodValidationPostProcessor();
    }
}

```

`User1Service3.java`
```java

package com.hello.demo.spring.validator.service;

import com.hello.demo.spring.validator.common.User1;
import org.springframework.stereotype.Service;
import org.springframework.validation.annotation.Validated;

// 需要配置MethodValidationPostProcessor
@Service
@Validated
public class User1Service3 {

    public void validatorUser(@Validated User1 user) {
        System.out.println(user);
    }
}

```

`User1ValidatorDemo.java`
```java

package com.hello.demo.spring.validator;

import com.hello.demo.spring.validator.common.User1;
import com.hello.demo.spring.validator.config.AppConfig;
import com.hello.demo.spring.validator.service.User1Service1;
import com.hello.demo.spring.validator.service.User1Service2;
import com.hello.demo.spring.validator.service.User1Service3;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class User1ValidatorDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

        User1 user1 = context.getBean("user1", User1.class);
        System.out.println(user1);

        User1Service1 user1Service1 = context.getBean(User1Service1.class);
        System.out.println("result1: " + user1Service1.validaUser1ByValidator(user1));

        User1Service2 user1Service2 = context.getBean(User1Service2.class);
        System.out.println("result2: " + user1Service2.validatorUser1((user1)));

        User1Service3 user1Service3 = context.getBean(User1Service3.class);
        user1Service3.validatorUser(user1);
    }
}
```

### 6.自定义类型校验器

`NotContainA.java`
```java
package com.hello.demo.spring.validator.common;

import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.*;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.RetentionPolicy.RUNTIME;


@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = NotContainAConstraintValidator.class) // 指定校验器
public @interface NotContainA {

	String message() default "不能包含字符A"; // 默认错误消息

	Class<?>[] groups() default { }; // 分组

	Class<? extends Payload>[] payload() default { }; // 负载

    // 指定多个时使用
	@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
	@Retention(RUNTIME)
	@Documented
	@interface List {

		jakarta.validation.constraints.NotNull[] value();
	}
}


```

`NotContainAConstraintValidator.java`

实现`ConstraintValidator`接口

```java
package com.hello.demo.spring.validator.common;

import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

public class NotContainAConstraintValidator implements ConstraintValidator<NotContainA, String> {

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        System.out.println("isValid");
        return !value.contains("A");
    }

    @Override
    public void initialize(NotContainA constraintAnnotation) {
        ConstraintValidator.super.initialize(constraintAnnotation);
    }
}

```

`User2.java`
```java
package com.hello.demo.spring.validator.common;

public class User2 {

    @NotContainA
    private String name;

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User2{" +
                "name='" + name + '\'' +
                '}';
    }
}

```

`User2Service.java`
```java
package com.hello.demo.spring.validator.service;

import com.hello.demo.spring.validator.common.User1;
import com.hello.demo.spring.validator.common.User2;
import jakarta.validation.ConstraintViolation;
import jakarta.validation.Validator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;


import java.util.Set;

@Service
public class User2Service {

    @Autowired
    private User2 user2;

    @Autowired
    private Validator validator;

    public boolean getUser2() {
        Set<ConstraintViolation<User2>> sets = this.validator.validate(this.user2);
        System.out.println(sets);
        System.out.println(this.user2);
        return !sets.isEmpty();

    }
}


```

`AppConfig.java`
```java
package com.hello.demo.spring.validator.config;

import com.hello.demo.spring.validator.common.User1;
import com.hello.demo.spring.validator.common.User2;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.validation.beanvalidation.LocalValidatorFactoryBean;
import org.springframework.validation.beanvalidation.MethodValidationPostProcessor;

@Configuration
@ComponentScan("com.hello")
public class AppConfig {
    
    @Bean
    public LocalValidatorFactoryBean validator() {
        return new LocalValidatorFactoryBean();
    }
    
    @Bean("user2")
    public User2 getUser2() {
        User2 user2 = new User2();
        user2.setName("helloA");
        return user2;
    }
}

```

`NotContainADemo.java`
```java
package com.hello.demo.spring.validator;

import com.hello.demo.spring.validator.config.AppConfig;
import com.hello.demo.spring.validator.service.User2Service;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class NotContainADemo {

    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);
        User2Service user2Service = context.getBean(User2Service.class);
        System.out.println(user2Service.getUser2());
    }
}

```