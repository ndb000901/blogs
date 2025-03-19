# servlet 基础示例

## 1. `@WebServlet` 注解

`@WebServlet` 是 **Jakarta Servlet 3.0**（之前的 Java EE 6）引入的一个注解，用于在 **不需要 web.xml** 配置的情况下，直接在 **Servlet 类** 上定义 Servlet，并映射到指定的 URL。  

在 Jakarta EE 8 及之后的版本中，该注解属于 **`jakarta.servlet.annotation`** 包，而在较早的 Java EE 版本（如 Java EE 6/7）中，它属于 **`javax.servlet.annotation`** 包。


`@WebServlet` 提供了多个可选参数，可用于精确控制 Servlet 的行为：

| 参数名         | 类型                | 说明 |
|--------------|-------------------|-------------|
| `value`      | `String` 或 `String[]` | 指定 Servlet 的 URL 映射（和 `urlPatterns` 作用相同，二选一） |
| `urlPatterns` | `String[]`         | 允许映射多个 URL |
| `name`       | `String`            | 指定 Servlet 名称（可选） |
| `loadOnStartup` | `int`             | 设置 Servlet 启动加载顺序 |
| `asyncSupported` | `boolean`       | 是否支持异步请求处理 |
| `initParams`  | `WebInitParam[]`   | 传递初始化参数 |

## 2.WebServlet注解与xml配置


### 1. 基础 Servlet 配置
对于如下的 `@WebServlet` 注解：
```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.getWriter().write("Hello, Jakarta Servlet!");
    }
}
```
对应的 `web.xml` 配置：
```xml
<web-app xmlns="http://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://jakarta.ee/xml/ns/jakartaee
                             http://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>com.example.HelloServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>

</web-app>
```
**解释：**
- `<servlet>` 定义 Servlet：
  - `servlet-name`：Servlet 的名称（可自定义）。
  - `servlet-class`：完整的 Servlet 类名。
- `<servlet-mapping>` 将 URL `/hello` 映射到 `HelloServlet`。

---

### 2. 多个 URL 映射
如果 `@WebServlet` 这样定义：
```java
@WebServlet(urlPatterns = {"/hello", "/greet"})
```
对应的 `web.xml`：
```xml
<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>

<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>/greet</url-pattern>
</servlet-mapping>
```
**解释：**
- 一个 Servlet 可以映射多个 URL，只需要定义多个 `<servlet-mapping>`。

---

### 3. `loadOnStartup`（启动时加载）
如果 `@WebServlet` 这样定义：
```java
@WebServlet(urlPatterns = "/start", loadOnStartup = 1)
```
对应的 `web.xml`：
```xml
<servlet>
    <servlet-name>StartupServlet</servlet-name>
    <servlet-class>com.example.StartupServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
```
**解释：**
- `load-on-startup`：值 **>0** 表示容器在启动时加载该 Servlet，值越小优先级越高。

---

## 4. `initParams`（初始化参数）
如果 `@WebServlet` 这样定义：
```java
@WebServlet(urlPatterns = "/config", initParams = {
    @WebInitParam(name = "username", value = "admin"),
    @WebInitParam(name = "password", value = "1234")
})
```
对应的 `web.xml`：
```xml
<servlet>
    <servlet-name>ConfigServlet</servlet-name>
    <servlet-class>com.example.ConfigServlet</servlet-class>
    <init-param>
        <param-name>username</param-name>
        <param-value>admin</param-value>
    </init-param>
    <init-param>
        <param-name>password</param-name>
        <param-value>1234</param-value>
    </init-param>
</servlet>
```
**解释：**
- `<init-param>` 用于传递初始化参数，Servlet 内部可用 `getServletConfig().getInitParameter("username")` 获取。

---

### 5. `asyncSupported`（异步处理）
如果 `@WebServlet` 这样定义：
```java
@WebServlet(urlPatterns = "/async", asyncSupported = true)
```
对应的 `web.xml`：
```xml
<servlet>
    <servlet-name>AsyncServlet</servlet-name>
    <servlet-class>com.example.AsyncServlet</servlet-class>
    <async-supported>true</async-supported>
</servlet>
```
**解释：**
- `async-supported`：`true` 允许 Servlet 进行 **异步处理**，通常用于 **异步 IO、长轮询等场景**。



## 3.`@WebFilter` 注解

`@WebFilter` 是 **Jakarta Servlet 3.0（Java EE 6）** 引入的注解，用于 **定义 Servlet 过滤器（Filter）**。  

过滤器（Filter）是一种 **拦截 HTTP 请求/响应** 的机制，可用于：
- **权限校验**（如登录拦截）
- **请求日志**（如 API 访问日志）
- **编码转换**（如 `request.setCharacterEncoding("UTF-8")`）
- **压缩响应**（如 GZIP 压缩）

---

### 代码示例
```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;

import java.io.IOException;

@WebFilter("/api/hello")
public class LogFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("LogFilter");
        chain.doFilter(request, response);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
        System.out.println("LogFilter init");
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
        System.out.println("LogFilter destroy");
    }
}
```
**等效的 `web.xml` 配置**
```xml
<filter>
    <filter-name>logFilter</filter-name>
    <filter-class>com.demo.LogFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>logFilter</filter-name>
    <url-pattern>/api/hello</url-pattern>
</filter-mapping>
```

---

### `@WebFilter` 的常见参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `filterName` | 过滤器名称 | `"authFilter"` |
| `urlPatterns` | 拦截 URL 模式 | `{"/api/*", "/admin/*"}` |
| `servletNames` | 绑定到指定的 Servlet | `{"MyServlet"}` |
| `dispatcherTypes` | 指定拦截的请求类型 | `{DispatcherType.REQUEST, DispatcherType.FORWARD}` |

---

### `dispatcherTypes` 详解

`dispatcherTypes` 决定了过滤器在哪些 **请求阶段** 触发：

| `DispatcherType` | 说明 |
|-----------------|------|
| `REQUEST` | **默认值**，拦截直接访问 Servlet 的请求 |
| `FORWARD` | 拦截 `RequestDispatcher.forward()` 转发的请求 |
| `INCLUDE` | 拦截 `RequestDispatcher.include()` 包含的请求 |
| `ERROR` | 拦截 `sendError()` 触发的错误请求 |
| `ASYNC` | 拦截异步请求 |

**示例：拦截所有请求，包括转发和错误请求**
```java
@WebFilter(urlPatterns = "/*", dispatcherTypes = {DispatcherType.REQUEST, DispatcherType.FORWARD, DispatcherType.ERROR})
```

---

### `doFilter()` 详解
#### **方法签名**
```java
void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
        throws IOException, ServletException;
```
- **`request`**：HTTP 请求对象，可转换为 `HttpServletRequest`
- **`response`**：HTTP 响应对象，可转换为 `HttpServletResponse`
- **`chain.doFilter(request, response)`**：继续执行下一个过滤器或 Servlet


### `@WebFilter` 生命周期
| 阶段 | 方法 | 说明 |
|------|------|------|
| **初始化** | `init(FilterConfig config)` | 过滤器被创建时调用（只执行一次） |
| **请求拦截** | `doFilter(request, response, chain)` | 处理每个 HTTP 请求 |
| **销毁** | `destroy()` | 服务器关闭或应用卸载时调用 |

## 4.源码

### 依赖

`pom.xml`
```xml
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/jakarta.platform/jakarta.jakartaee-web-api -->
        <dependency>
            <groupId>jakarta.platform</groupId>
            <artifactId>jakarta.jakartaee-web-api</artifactId>
            <version>10.0.0</version>
            <scope>provided</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/com.google.code.gson/gson -->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.12.1</version>
        </dependency>


    </dependencies>
```

### 控制器

`HelloController.java`
```java
package com.hello.demo.servlet.controllers;

import com.google.gson.Gson;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;


@WebServlet("/api/hello")
public class HelloController extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {

        resp.setContentType("application/json");
        resp.setCharacterEncoding("UTF-8");

        // 创建要返回的 JSON 数据
        HelloResponse helloResponse = new HelloResponse("Hello, World!");

        // 使用 Gson 将 Java 对象转换为 JSON 字符串
        Gson gson = new Gson();

        String json = gson.toJson(helloResponse);

        // 将 JSON 字符串写入响应
        PrintWriter out = resp.getWriter();
        out.print(json);
        out.flush();
        System.out.println("invoke /api/hello");
    }

    // 定义一个简单的 Java 类来表示 JSON 数据
    static class HelloResponse {
        private String message;

        public HelloResponse(String message) {
            this.message = message;
        }

        public String getMessage() {
            return message;
        }
    }
}

```

### 过滤器

`LogFilter.java`
```java
package com.hello.demo.servlet.filters;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;

import java.io.IOException;

@WebFilter("/api/hello")
public class LogFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("LogFilter");
        chain.doFilter(request, response);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
        System.out.println("LogFilter init");
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
        System.out.println("LogFilter destroy");
    }
}

```

### 部署

```bash

mvn clean package

# 将war包拷贝的tomcat的webapps 目录下
cp xxx.war /tomcat/path/webapps
```