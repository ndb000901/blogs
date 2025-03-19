# SpringMVC-过滤器

Spring MVC 主要依赖 **Servlet 过滤器（Filter）** 进行请求处理。在 Spring MVC 应用中，常见的过滤器（Filters）包括以下几类：  

---

## **1. Spring MVC 自带的核心过滤器**
Spring MVC 本身提供了一些重要的过滤器，主要用于安全性、字符编码、多部分请求解析等。

### **1.1 `CharacterEncodingFilter`（字符编码过滤器）**
**作用**：
- 解决 **POST 请求** 乱码问题，确保所有请求和响应都使用 **统一的字符编码（如 UTF-8）**。

**使用方式**：
```java
@Bean
public FilterRegistrationBean<CharacterEncodingFilter> characterEncodingFilter() {
    FilterRegistrationBean<CharacterEncodingFilter> registrationBean = new FilterRegistrationBean<>();
    CharacterEncodingFilter filter = new CharacterEncodingFilter();
    filter.setEncoding("UTF-8");
    filter.setForceEncoding(true);
    registrationBean.setFilter(filter);
    registrationBean.addUrlPatterns("/*"); // 适用于所有请求
    return registrationBean;
}
```

---

### **1.2 `HiddenHttpMethodFilter`（支持 `PUT`, `DELETE` 等 HTTP 方法）**
**作用**：
- 由于 HTML 表单默认只支持 `GET` 和 `POST`，`HiddenHttpMethodFilter` 允许在 `POST` 请求中添加 `_method` 参数，以 **模拟 `PUT`、`DELETE` 请求**，便于 RESTful API 设计。

**示例**（前端提交 `DELETE` 请求）：  
```html
<form action="/api/user/123" method="POST">
    <input type="hidden" name="_method" value="DELETE"/>
    <button type="submit">删除用户</button>
</form>
```
Spring MVC 处理时，会将请求 **转换为 `DELETE` 方法**。

**启用方式**（Spring Boot 自动配置，如果是纯 Spring MVC 需要手动配置）：
```java
@Bean
public HiddenHttpMethodFilter hiddenHttpMethodFilter() {
    return new HiddenHttpMethodFilter();
}
```

---

### **1.3 `HttpPutFormContentFilter`（处理 `PUT` 请求的表单数据）**
**作用**：
- 允许 `PUT` 请求中的表单数据 **自动解析并封装到 `@RequestParam` 或 `@ModelAttribute`**。

**默认情况下，`PUT` 请求的 `application/x-www-form-urlencoded` 数据不会被 Spring MVC 自动解析**，这个过滤器可以解决这个问题。

**启用方式**：
```java
@Bean
public HttpPutFormContentFilter httpPutFormContentFilter() {
    return new HttpPutFormContentFilter();
}
```

---

### **1.4 `RequestContextFilter`（绑定 `RequestContext` 到当前线程）**
**作用**：
- 允许在 **非 Spring MVC 组件（如 Service 层）** 也能获取 `HttpServletRequest`、`HttpSession` 等对象。
- 绑定 `Locale`，支持国际化功能。

**启用方式**：
```java
@Bean
public RequestContextFilter requestContextFilter() {
    return new RequestContextFilter();
}
```

---

### **1.5 `FormContentFilter`（替代 `HttpPutFormContentFilter`）**
**作用**：
- 在 Spring Boot 2.2 及以上版本，`HttpPutFormContentFilter` 被 `FormContentFilter` 取代，功能类似。

**启用方式**：
```java
@Bean
public FormContentFilter formContentFilter() {
    return new FormContentFilter();
}
```

---

## **2. Spring Security 相关的过滤器**
如果应用使用 **Spring Security**，则会有一系列安全相关的过滤器，例如：

### **2.1 `SecurityContextPersistenceFilter`**
- 负责维护 `SecurityContext`，在每次请求之间存储和恢复用户的身份信息。

### **2.2 `UsernamePasswordAuthenticationFilter`**
- 处理用户的 **用户名/密码登录** 认证逻辑。

### **2.3 `BasicAuthenticationFilter`**
- 处理 HTTP **Basic 认证**。

### **2.4 `CsrfFilter`**
- 负责 **CSRF（跨站请求伪造）** 保护。

---

## **3. Servlet 规范的通用过滤器（Spring MVC 可用）**
除了 Spring MVC 自带的过滤器，**Servlet 规范** 本身提供了一些通用的 `Filter`，可以用于 Spring MVC ：

### **3.1 `CorsFilter`（跨域请求过滤器）**
**作用**：
- 允许指定哪些域名可以跨域访问 API，防止 **CORS（跨域资源共享）** 限制。

**Spring 配置**：
```java
@Bean
public CorsFilter corsFilter() {
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    CorsConfiguration config = new CorsConfiguration();
    config.addAllowedOrigin("*");  // 允许所有域名
    config.addAllowedMethod("*");  // 允许所有方法 (GET, POST, DELETE, etc.)
    config.addAllowedHeader("*");  // 允许所有头
    source.registerCorsConfiguration("/**", config);
    return new CorsFilter(source);
}
```

---

### **3.2 `ShallowEtagHeaderFilter`（Etag 过滤器，提高缓存命中率）**
**作用**：
- 计算 **请求内容的 ETag（实体标签）**，如果客户端已经缓存了相同的内容，直接返回 `304 Not Modified`，减少网络带宽使用。

**启用方式**：
```java
@Bean
public FilterRegistrationBean<ShallowEtagHeaderFilter> shallowEtagHeaderFilter() {
    FilterRegistrationBean<ShallowEtagHeaderFilter> registrationBean = new FilterRegistrationBean<>();
    registrationBean.setFilter(new ShallowEtagHeaderFilter());
    registrationBean.addUrlPatterns("/*");
    return registrationBean;
}
```

---

### **3.3 `CompressionFilter`（GZIP 压缩过滤器）**
**作用**：
- 在 **Spring MVC 响应数据时自动压缩**，减少带宽消耗，加快页面加载。

**配置方式**：
如果使用 Tomcat，可在 `server.xml` 启用 GZIP：
```xml
<Connector port="8080" protocol="HTTP/1.1" compression="on" compressionMinSize="1024" noCompressionUserAgents="gozilla, traviata" compressibleMimeType="text/html,text/xml,text/plain,text/css,text/javascript,application/javascript,application/json"/>
```
如果使用 Spring 过滤器：
```java
@Bean
public FilterRegistrationBean<GzipFilter> gzipFilter() {
    FilterRegistrationBean<GzipFilter> registrationBean = new FilterRegistrationBean<>();
    registrationBean.setFilter(new GzipFilter());
    registrationBean.addUrlPatterns("/*");
    return registrationBean;
}
```

---

## **4. 自定义过滤器**
如果 Spring MVC 现有的过滤器无法满足需求，可以自定义 `Filter`。

### **示例：记录请求执行时间**
```java
@WebFilter("/*")  // 适用于所有请求
public class ExecutionTimeFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        long startTime = System.currentTimeMillis();
        chain.doFilter(request, response); // 继续执行
        long duration = System.currentTimeMillis() - startTime;
        System.out.println("请求耗时：" + duration + " 毫秒");
    }
}
```
