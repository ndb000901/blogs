# Spring-常用注解

## 1.`@RequestMapping`


`@RequestMapping` 可以作用在 **类** 和 **方法** 上：
- **类级别**：定义控制器的基本 URL 前缀，作用于所有该控制器中的方法。
- **方法级别**：定义具体的 URL 处理逻辑。

```java
@RestController
@RequestMapping("/users")  // 类级别
public class UserController {

    @RequestMapping("/list")  // 方法级别
    public List<String> getUsers() {
        return List.of("Alice", "Bob", "Charlie");
    }
}
```

### 指定 HTTP 请求方法
默认情况下，`@RequestMapping` 接受所有 HTTP 方法，可以使用 `method` 属性指定请求方式：
```java
@RestController
@RequestMapping("/users")
public class UserController {

    @RequestMapping(value = "/create", method = RequestMethod.POST)
    public String createUser() {
        return "User created";
    }
}
```

### 可使用以下注解替代

- `@GetMapping`（GET）
- `@PostMapping`（POST）
- `@PutMapping`（PUT）
- `@DeleteMapping`（DELETE）
- `@PatchMapping`（PATCH）

```java
@RestController
@RequestMapping(value = "/api/method")
public class MethodController {

    @RequestMapping(value = "/get", method = RequestMethod.GET)
    public String getMethod() {
        System.out.println("MethodController.getMethod run...");
        return "MethodController.getMethod";
    }

    @PostMapping(value = "/post")
    public String postMethod() {
        System.out.println("MethodController.postMethod run...");
        return "MethodController.postMethod";
    }

    @PutMapping(value = "/put")
    public String putMethod() {
        System.out.println("MethodController.putMethod run...");
        return "MethodController.putMethod";
    }

    @DeleteMapping("/delete")
    public String deleteMethod() {
        System.out.println("MethodController.deleteMethod run...");
        return "MethodController.deleteMethod";
    }

    @PatchMapping(value = "/patch")
    public String patchMethod() {
        System.out.println("MethodController.patchMethod run...");
        return "MethodController.patchMethod";
    }

}

```

### `@RequestMapping`注解的params属性

- "param"：要求请求映射所匹配的请求必须携带param请求参数
- "!param"：要求请求映射所匹配的请求必须不能携带param请求参数
- "param=value"：要求请求映射所匹配的请求必须携带param请求参数且param=value
- "param!=value"：要求请求映射所匹配的请求必须携带param请求参数但是param!=value

```java
    @RequestMapping(value = "/params", method = RequestMethod.GET, params = {"user=admin", "password!=123456", "token"})
    public String testParams(String user, String password, String token) {
        System.out.println("MethodController.testParams run...");
        System.out.println("user: " + user + ", password: " + password + ", token: " + token);
        return "MethodController.testParams: " + "user: " + user + ", password: " + password + ", token: " + token;
    }
```

### `@RequestMapping`注解的headers属性

- "header"：要求请求映射所匹配的请求必须携带header请求头信息
- "!header"：要求请求映射所匹配的请求必须不能携带header请求头信息
- "header=value"：要求请求映射所匹配的请求必须携带header请求头信息且header=value
- "header!=value"：要求请求映射所匹配的请求必须携带header请求头信息且header!=value

```java
    @RequestMapping(value = "/headers", method = RequestMethod.GET, headers = {"user=admin", "password!=123456", "token"})
    public String testHeaders(
            @RequestHeader("user") String user,
            @RequestHeader("password") String password,
            @RequestHeader("token") String token
    ) {
        System.out.println("MethodController.testHeaders run...");
        System.out.println("user: " + user + ", password: " + password + ", token: " + token);
        return "MethodController.testHeaders  " + "user: " + user + ", password: " + password + ", token: " + token;
    }
```

### 路径通配符

- ？：表示任意的单个字符
- *：表示任意的0个或多个字符
- \**：表示任意的一层或多层目录
- 注意：在使用\**时，只能使用/**/xxx的方式

### 路径占位符

```java
    @RequestMapping(value = "/path/{id}", method = RequestMethod.GET)
    public String testPath(@PathVariable("id") String id) {
        System.out.println("MethodController.testPath run...");
        return "MethodController.testPath: " + id;
    }
```

## 2.获取请求参数

### 通过HttpServletRequest对象获取请求参数

```java

package com.hello.demo.spring.mvc.hello.xml.controllers;

import com.hello.demo.spring.mvc.hello.xml.common.User;
import jakarta.servlet.http.HttpServletRequest;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/api/params")
public class ParamsController {

    // 通过 HttpServletRequest 获取参数
    @RequestMapping(value = "/request", params = {"name", "password"})
    public String testRequest(HttpServletRequest request) {
        String name = request.getParameter("name");
        String password = request.getParameter("password");
        System.out.println("ParamsController.testRequest " + "name: " + name + " password" + password);
        return "ParamsController.testRequest " + "name:" + name + " password" + password;
    }
    
}

```

### 通过控制器参数获取请求参数

```java
package com.hello.demo.spring.mvc.hello.xml.controllers;

import com.hello.demo.spring.mvc.hello.xml.common.User;
import jakarta.servlet.http.HttpServletRequest;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/api/params")
public class ParamsController {

    
    // 通过控制器方法
    @RequestMapping(value = "/method-params", params = {"name", "password"})
    public String testMethodParams(String name, String password) {
        System.out.println("ParamsController.testMethodParams " + "name: " + name + " password" + password);
        return "ParamsController.testMethodParams " + "name:" + name + " password" + password;
    }

}

```


### 通过`@RequestParam`获取请求参数

- value：指定为形参赋值的请求参数的参数名
- required：设置是否必须传输此请求参数，默认值为true
若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；若设置为false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null
- defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值

```java
package com.hello.demo.spring.mvc.hello.xml.controllers;

import com.hello.demo.spring.mvc.hello.xml.common.User;
import jakarta.servlet.http.HttpServletRequest;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/api/params")
public class ParamsController {
    
    // 通过@RequestParam获取参数
    @RequestMapping(value = "/request-param", params = { "password" })
    public String testRequestParam(
            @RequestParam(value = "name", required = false, defaultValue = "admin") String name,
            @RequestParam(value = "password", required = true) String password
    ) {
        System.out.println("ParamsController.testRequestParam " + "name: " + name + " password" + password);
        return "ParamsController.testRequestParam " + "name:" + name + " password" + password;
    }

}

```

### 通过对象获取请求参数

`User.java`
```java
package com.hello.demo.spring.mvc.hello.xml.common;


public class User {

    private String name;

    private String password;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}

```

`ParamsController.java`
```java

package com.hello.demo.spring.mvc.hello.xml.controllers;

import com.hello.demo.spring.mvc.hello.xml.common.User;
import jakarta.servlet.http.HttpServletRequest;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/api/params")
public class ParamsController {
    
    // 通过对象获取参数
    @RequestMapping(value = "/pojo", params = { "name", "password" })
    public String testPojo(User user) {
        System.out.println("ParamsController.testPojo " + user);
        return "ParamsController.testPojo " + user;
    }
}


```


## 3.`@RequestHeader`获取请求头 

参数与`@RequestParam` 一致

```java
package com.hello.demo.spring.mvc.hello.xml.controllers;

import com.hello.demo.spring.mvc.hello.xml.common.User;
import jakarta.servlet.http.HttpServletRequest;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/api/params")
public class ParamsController {
    
    // 获取请求头
    @RequestMapping(value = "/headers", headers = {"password"})
    public String testHeaders(
            @RequestHeader(value = "name", required = false, defaultValue = "admin") String name,
            @RequestHeader(value = "password", required = true) String password
    ) {
        System.out.println("ParamsController.testHeaders " + "name: " + name + " password" + password);
        return "ParamsController.testHeaders " + "name:" + name + " password" + password;
    }


}

```

## 4.`@CookieValue`获取Cookie

参数与`@RequestParam`一致

```java
package com.hello.demo.spring.mvc.hello.xml.controllers;

import com.hello.demo.spring.mvc.hello.xml.common.User;
import jakarta.servlet.http.HttpServletRequest;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/api/params")
public class ParamsController {

    
    // 获取cookies
    @RequestMapping(value = "/cookies")
    public String testCookies(
            @CookieValue(value = "name", required = true, defaultValue = "admin") String name,
            @CookieValue(value = "password", required = true) String password
    ) {
        System.out.println("ParamsController.testCookies " + "name: " + name + " password" + password);
        return "ParamsController.testCookies " + "name:" + name + " password: " + password;
    }
    
}

```


## 5.`@RequestBody`
@RequestBody可以获取请求体，需要在控制器方法设置一个形参，使用@RequestBody进行标识

```java
package com.hello.demo.spring.mvc.hello.xml.controllers;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("/api/request")
@ResponseBody
public class RequestController {

    @RequestMapping("/request-body")
    public String testRequestBody(@RequestBody String body) {

        System.out.println("body: " + body);
        return "RequestController.testRequestBody: " + body;
    }
}


```


## 6.`RequestEntity`

`RequestEntity` 是封装请求报文的一种类型，需要在控制器方法的形参中设置该类型的形参，当前请求的请求报文就会赋值给该形参，可以通过getHeaders()获取请求头信息，通过getBody()获取请求体信息

```java
package com.hello.demo.spring.mvc.hello.xml.controllers;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("/api/request")
@ResponseBody
public class RequestController {
    
    @RequestMapping("/request-entity")
    public String testRequestEntity(RequestEntity<String> requestEntity) {
        System.out.println("body: " + requestEntity.getBody());
        System.out.println("headers: " + requestEntity.getHeaders());
        return "RequestController.testRequestEntity";
    }
    
}

```

## 7.`ResponseEntity`

`ResponseEntity` 用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文
```java
package com.hello.demo.spring.mvc.hello.xml.controllers;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("/api/request")
@ResponseBody
public class RequestController {

    
    @PostMapping("/response-entity")
    public ResponseEntity<String> testResponseEntity(RequestEntity<String> entity) {

        System.out.println("RequestController.testResponseEntity");
        return ResponseEntity.ok("Rec: " + entity);
    }


}

```

## 8.`@ResponseBody`

@ResponseBody用于标识一个控制器方法/类，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

## 9.`@RestController`

@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，
就相当于为类添加了@Controller注解，并且为其中的每个方法添加了@ResponseBody注解

