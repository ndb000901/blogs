# SpringMVC-Dispatcher 流程分析

`DispatcherServlet` 是 **Spring MVC 的核心组件**，作为 **前端控制器（Front Controller）**，负责处理所有 HTTP 请求，并协调整个 Spring MVC 流程。  
它的主要职责是 **请求分发、调用处理器、视图解析** 等。

---

## **1. DispatcherServlet 工作流程**
Spring MVC 的执行流程可以大致分为以下 **7 个步骤**：

### **① 接收请求**
当客户端发送请求时，`DispatcherServlet` 作为 **Servlet 容器（如 Tomcat）** 的前端控制器，拦截所有匹配的请求（通过 `web.xml` 或 `SpringBoot` 配置）。
- URL 映射是由 `web.xml` 或 `@WebServlet` 配置的：
  ```xml
  <servlet>
      <servlet-name>dispatcher</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  </servlet>

  <servlet-mapping>
      <servlet-name>dispatcher</servlet-name>
      <url-pattern>/</url-pattern>
  </servlet-mapping>
  ```
  这样 `DispatcherServlet` 就会拦截 **所有请求**（`/*`）。

---

### **② 解析请求，寻找合适的处理器（Handler Mapping）**
`DispatcherServlet` 通过 **HandlerMapping** 组件找到 **具体的处理器（Controller）**，它的作用是根据 **请求 URL** 查找 **匹配的处理器**。

Spring 内置了几种 `HandlerMapping`：
1. **RequestMappingHandlerMapping**（默认）：基于 `@RequestMapping`
2. **SimpleUrlHandlerMapping**：基于 `properties` 配置
3. **BeanNameUrlHandlerMapping**：基于 Bean 的名称

示例：
```java
@Controller
@RequestMapping("/user")
public class UserController {
    @RequestMapping("/info")
    public String userInfo() {
        return "userInfo"; // 视图名称
    }
}
```
`DispatcherServlet` 解析出 `/user/info` 对应的是 `UserController.userInfo()` 方法。

---

### **③ 调用处理器适配器（HandlerAdapter）**
找到 `Controller` 之后，Spring 还需要一个适配器来 **调用它的 `@RequestMapping` 方法**。

Spring 提供了 **多个 HandlerAdapter**，用于适配不同的 `Controller` 类型：
1. **RequestMappingHandlerAdapter**（默认）：支持 `@RequestMapping`
2. **HttpRequestHandlerAdapter**：支持 `HttpRequestHandler`
3. **SimpleControllerHandlerAdapter**：支持 `Controller` 接口（Spring 2.x 时代）

示例：
```java
@RestController
@RequestMapping("/hello")
public class HelloController {
    @GetMapping("/say")
    public String sayHello() {
        return "Hello, Spring MVC!";
    }
}
```
这里 `DispatcherServlet` 发现 `/hello/say` 匹配 `HelloController.sayHello()`，然后调用 **RequestMappingHandlerAdapter** 来执行它。

---

### **④ 处理器执行，返回 ModelAndView**
`Controller` 方法执行后，会返回数据给 `DispatcherServlet`：
- 如果是 **`String`**，则视为 **视图名称**，由 `ViewResolver` 解析；
- 如果是 **`ModelAndView`**，包含 **数据 + 视图名称**；
- 如果是 **`@ResponseBody`**，则直接返回 JSON 数据。

示例：
```java
@RequestMapping("/test")
public ModelAndView test() {
    ModelAndView mv = new ModelAndView();
    mv.setViewName("testView");
    mv.addObject("message", "Hello Spring MVC");
    return mv;
}
```
这里 `DispatcherServlet` 得到：
```plaintext
View Name: testView
Model: { message: "Hello Spring MVC" }
```

---

### **⑤ 视图解析（ViewResolver）**
`DispatcherServlet` 通过 **ViewResolver 解析视图**，找到对应的 `JSP/Thymeleaf/Freemarker` 等文件。

常见的 `ViewResolver`：
1. **InternalResourceViewResolver**（JSP）
2. **ThymeleafViewResolver**（Thymeleaf）
3. **FreeMarkerViewResolver**（FreeMarker）

示例：
```java
@Bean
public InternalResourceViewResolver viewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setPrefix("/WEB-INF/views/");
    resolver.setSuffix(".jsp");
    return resolver;
}
```
如果 `Controller` 返回 `"testView"`，那么 `DispatcherServlet` 解析出：
```plaintext
/WEB-INF/views/testView.jsp
```

---

### **⑥ 视图渲染**
`DispatcherServlet` 渲染视图，将 **模型数据（Model）** 填充到 **JSP/Thymeleaf** 页面：
```jsp
<!-- testView.jsp -->
<html>
<body>
    <h1>${message}</h1>
</body>
</html>
```

---

### **⑦ 响应数据给客户端**
最终 `DispatcherServlet` 将 **渲染后的 HTML 页面** 或 **JSON 响应** 发送回客户端。

示例：
```json
{
    "message": "Hello Spring MVC"
}
```

---

## **2. DispatcherServlet 的核心组件**
| **组件**               | **作用** |
|----------------------|--------|
| **HandlerMapping**  | 根据请求 URL 选择合适的 `Controller` |
| **HandlerAdapter**  | 适配 `Controller`，调用其方法 |
| **ViewResolver**    | 解析 `Controller` 返回的视图名称 |
| **ModelAndView**    | 存储数据模型和视图名称 |
| **View**            | 视图对象（如 JSP/Thymeleaf），渲染页面 |

---

## **3. DispatcherServlet 执行过程总结**
```plaintext
1. 客户端请求被 DispatcherServlet 拦截
2. DispatcherServlet 通过 HandlerMapping 找到合适的 Controller
3. DispatcherServlet 通过 HandlerAdapter 调用 Controller 方法
4. Controller 方法执行后返回 ModelAndView
5. DispatcherServlet 通过 ViewResolver 解析视图名称
6. DispatcherServlet 渲染视图，并将 HTML 响应返回给客户端
```

---

## **4. DispatcherServlet 初始化过程**
`DispatcherServlet` 也是一个 `Servlet`，它的初始化流程如下：
1. **调用 `init()` 方法**
2. **加载 `WebApplicationContext`（Spring MVC 容器）**
3. **初始化 `HandlerMapping`、`HandlerAdapter`、`ViewResolver` 等组件**
4. **准备拦截 HTTP 请求**

---

## **5. 重点源码分析**
`DispatcherServlet` 的核心方法：
```java
@Override
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    // 1. 解析 HandlerMapping，找到 Controller
    HandlerExecutionChain mappedHandler = getHandler(request);

    // 2. 找到适配的 HandlerAdapter
    HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

    // 3. 执行 Controller 方法，获取 ModelAndView
    ModelAndView mv = ha.handle(request, response, mappedHandler.getHandler());

    // 4. 解析视图
    View view = resolveViewName(mv.getViewName());

    // 5. 渲染视图，返回 HTML 响应
    view.render(mv.getModel(), request, response);
}
```

---

