# SpringMVC-拦截器

Spring MVC 拦截器（Interceptor）是一个用于拦截和处理 HTTP 请求的机制，它在请求处理的不同阶段提供了钩子，可以用于处理前后逻辑，比如权限检查、日志记录、性能监控等。拦截器类似于 Servlet 的过滤器（Filter），但是它是专门为 Spring MVC 框架设计的，并且能够访问 Spring 的请求处理机制、模型、控制器等。

## 1.拦截器的工作原理

Spring MVC 拦截器的工作原理可以分为三个步骤：
1. **请求到达控制器之前**，拦截器可以做一些操作，比如权限验证。
2. **请求处理后、视图渲染之前**，可以进行数据修改或者记录日志等操作。
3. **视图渲染后**，可以做一些清理操作，比如资源释放。

Spring MVC 拦截器的执行顺序是按配置的拦截器的声明顺序来执行的，`preHandle` 方法是先执行的，`postHandle` 方法是后执行的，`afterCompletion` 方法是所有处理完成后执行的。

## 2.拦截器接口

Spring MVC 的拦截器接口是 `HandlerInterceptor`，它包含三个方法：

1. **`preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)`**  
   在请求处理之前调用。返回 `true` 表示继续流程，返回 `false` 会阻止请求进一步处理。

   ```java
   boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;
   ```

   - **返回值：**  
     - `true`：继续请求处理流程
     - `false`：请求终止，不会调用后续的拦截器和目标方法。

2. **`postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)`**  
   在请求处理之后，视图渲染之前调用。可以通过修改 `ModelAndView` 对象来调整视图信息。

   ```java
   void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception;
   ```

3. **`afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)`**  
   在整个请求处理完毕之后，视图渲染之后调用。此时，视图已经生成，可以进行一些资源清理工作。

   ```java
   void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception;
   ```

## 3.使用拦截器

1. **创建拦截器**

   你需要实现 `HandlerInterceptor` 接口，重写三个方法：

   ```java
    package com.hello.demo.spring.mvc.hello.xml.interceptor;
    
    import com.hello.demo.spring.mvc.hello.xml.common.LogData;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import org.apache.logging.log4j.LogManager;
    import org.apache.logging.log4j.Logger;
    import org.springframework.web.servlet.HandlerInterceptor;
    import org.springframework.web.servlet.ModelAndView;
    
    public class LogInterceptor implements HandlerInterceptor {
    
        private final static Logger logger = LogManager.getLogger(LogInterceptor.class);
    
        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
            LogData data = new LogData();
            data.setIp(request.getRemoteAddr());
            data.setMethod(request.getMethod());
            data.setPath(request.getServletPath());
            logger.info("LogInterceptor.preHandle: " + data);
            return true;
        }
    
        @Override
        public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
            HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
            logger.info("LogInterceptor.postHandle");
        }
    
        @Override
        public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
            HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
            logger.info("LogInterceptor.afterCompletion");
        }
    }

   ```

2. **注册拦截器**

   拦截器需要在 Spring 的配置类中注册。通常是在 `WebMvcConfigurer` 中配置拦截器，具体方法是重写 `addInterceptors` 方法。

   ```java
   @Configuration
   public class WebConfig implements WebMvcConfigurer {
   
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new LogInterceptor())
                   .addPathPatterns("/api/*") // 对所有 /api/* 请求应用拦截器
                   .excludePathPatterns("/api/login", "/api/logout"); // 排除某些路径
       }
   }
   ```
   xml配置
    ```xml
        <!--拦截器配置-->
    <!--接口请求日志-->
    <bean id="logInterceptor" class="com.hello.demo.spring.mvc.hello.xml.interceptor.LogInterceptor"/>
    <!--鉴权-->
    <bean id="authInterceptor" class="com.hello.demo.spring.mvc.hello.xml.interceptor.AuthInterceptor"/>
    <!--接口执行时间-->
    <bean id="executionInterceptor" class="com.hello.demo.spring.mvc.hello.xml.interceptor.ExecutionTimeInterceptor"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/api/interceptor/*"/>
            <ref bean="logInterceptor"/>
        </mvc:interceptor>
        <mvc:interceptor>
            <mvc:mapping path="/api/interceptor/*"/>
            <ref bean="authInterceptor"/>
        </mvc:interceptor>
        <mvc:interceptor>
            <mvc:mapping path="/api/interceptor/*"/>
            <ref bean="executionInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
    ```

   其中：
   - `addPathPatterns("/api/*")`：配置需要拦截的请求路径。
   - `excludePathPatterns("/api/login", "/api/logout")`：配置不需要拦截的请求路径。

3. **拦截器的执行顺序**

   如果配置了多个拦截器，它们会按注册的顺序依次执行。`preHandle` 按照声明顺序从上到下执行，`postHandle` 和 `afterCompletion` 则按照声明顺序相反，从下到上执行。


## 4.代码示例

`LogInterceptor.java` 用于打印调用日志

```java
package com.hello.demo.spring.mvc.hello.xml.interceptor;

import com.hello.demo.spring.mvc.hello.xml.common.LogData;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

public class LogInterceptor implements HandlerInterceptor {

    private final static Logger logger = LogManager.getLogger(LogInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        LogData data = new LogData();
        data.setIp(request.getRemoteAddr());
        data.setMethod(request.getMethod());
        data.setPath(request.getServletPath());
        logger.info("LogInterceptor.preHandle: " + data);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
        logger.info("LogInterceptor.postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
        logger.info("LogInterceptor.afterCompletion");
    }
}

```

`AuthInterceptor.java` 用于鉴权

```java
package com.hello.demo.spring.mvc.hello.xml.interceptor;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import java.util.Objects;

public class AuthInterceptor implements HandlerInterceptor {

    private final static Logger logger = LogManager.getLogger(AuthInterceptor.class);

    private final String tokenType = "Bearer";

    private final String token = "admin123456";

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        boolean result = false;
        String tokenStr = request.getHeader("Authorization");
        String[] tokenArray = tokenStr.split(" ");
        if (tokenArray.length == 2 && tokenArray[0].equals(tokenType) && tokenArray[1].equals(token)) {
            logger.info("AuthInterceptor.preHandle true");
            return true;
        }
        logger.info("AuthInterceptor.preHandle false");
        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
        logger.info("AuthInterceptor.postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
        logger.info("AuthInterceptor.afterCompletion");
    }
}

```

`ExecutionTimeInterceptor.java` 用于记录耗时

```java
package com.hello.demo.spring.mvc.hello.xml.interceptor;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

public class ExecutionTimeInterceptor implements HandlerInterceptor {

    private final static Logger logger = LogManager.getLogger(ExecutionTimeInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        long startTime = System.currentTimeMillis();
        request.setAttribute("startTime", startTime);
        logger.info("ExecutionTimeInterceptor.preHandle");
        return HandlerInterceptor.super.preHandle(request, response, handler);
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        long duration = System.currentTimeMillis() - (Long)request.getAttribute("startTime");
        logger.info("ExecutionTimeInterceptor.postHandle, duration: " + duration);
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        long duration = System.currentTimeMillis() - (Long)request.getAttribute("startTime");
        logger.info("ExecutionTimeInterceptor.afterCompletion, duration: " + duration);
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}

```

`InterceptorController.java`
```java

package com.hello.demo.spring.mvc.hello.xml.controllers;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;


@RestController
@RequestMapping(value = "/api/interceptor")
public class InterceptorController {

    private final static Logger logger = LogManager.getLogger(InterceptorController.class);

    @GetMapping("/test")
    public String testInterceptor() {
        logger.info("InterceptorController.testInterceptor run...");
        return "InterceptorController.testInterceptor";
    }
}

```

`springMVC.xml`
```xml

    <!--拦截器配置-->
    <!--接口请求日志-->
    <bean id="logInterceptor" class="com.hello.demo.spring.mvc.hello.xml.interceptor.LogInterceptor"/>
    <!--鉴权-->
    <bean id="authInterceptor" class="com.hello.demo.spring.mvc.hello.xml.interceptor.AuthInterceptor"/>
    <!--接口执行时间-->
    <bean id="executionInterceptor" class="com.hello.demo.spring.mvc.hello.xml.interceptor.ExecutionTimeInterceptor"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/api/interceptor/*"/>
            <ref bean="logInterceptor"/>
        </mvc:interceptor>
        <mvc:interceptor>
            <mvc:mapping path="/api/interceptor/*"/>
            <ref bean="authInterceptor"/>
        </mvc:interceptor>
        <mvc:interceptor>
            <mvc:mapping path="/api/interceptor/*"/>
            <ref bean="executionInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
```