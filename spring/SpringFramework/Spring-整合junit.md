# Spring-整合junit

## 1.依赖

```xml
<dependencies>
            <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>6.2.3</version>
    </dependency>
    
    <!-- https://mvnrepository.com/artifact/org.junit.jupiter/junit-jupiter-api -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.12.0</version>
        <scope>test</scope>
    </dependency>
    
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>6.2.3</version>
        <scope>test</scope>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.junit.jupiter/junit-jupiter-engine -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>5.12.0</version>
        <scope>test</scope>
    </dependency>
    
</dependencies>
```


## 2.代码示例

`SpringXmlTest.java`
```java
package com.hello.demo.spring.junit;


import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;

// 方法1
@ExtendWith(SpringExtension.class)
//@ContextConfiguration("classpath:bean.xml")
@ContextConfiguration(classes = UserService.class)

// 方法2
// @SpringJUnitConfig(locations = "classpath:bean.xml")
public class SpringXmlTest {

    @Autowired
    private UserService userService;


    @Test
    public void test() {
        this.userService.getUser();
    }

}
```