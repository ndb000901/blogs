# servlet 生命周期

Servlet 的生命周期（Lifecycle）是 **Servlet 在 Web 容器（如 Tomcat、Jetty）中的完整执行过程**，包括创建、初始化、处理请求、销毁等阶段。  

## **1. Servlet 生命周期的 5 个阶段**
Servlet 生命周期包含以下 5 个主要阶段：
1. **加载（Loading）**
2. **实例化（Instantiation）**
3. **初始化（Initialization）**
4. **处理请求（Request Handling）**
5. **销毁（Destruction）**

---

## **2. Servlet 生命周期详细解析**
### **（1）Servlet 加载 & 实例化**
  - **默认情况**：当 Servlet **第一次接收请求**时，Servlet 容器会**动态加载**该 Servlet 类并实例化。
  - **`loadOnStartup` 指定**：如果 `load-on-startup > 0`，则容器**在服务器启动时就会加载并实例化 Servlet**。
- **关键点**：
  - 由 Servlet 容器（Tomcat/Jetty 等）**通过反射创建 Servlet 实例**。
  - 仅会**创建一个实例**（单例模式）。
  - 只会执行**一次**。

```java
@WebServlet(
        loadOnStartup = 1,
        urlPatterns = "/api/hello"
)
public class HelloController extends HttpServlet {

    public HelloController() {
        System.out.println("HelloController 构造函数执行");
    }
}
```
**等效的 `web.xml`：**
```xml
<servlet>
    <servlet-name>HelloController</servlet-name>
    <servlet-class>com.demo.HelloController</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
```

---

### **（2）Servlet 初始化（`init()` 方法）**

  - 在 Servlet 实例化后，容器会调用 `init(ServletConfig config)` 进行初始化。
  - `init()` 只会被调用 **一次**（除非服务器重启或重新部署）。
- **用途**：
  - 用于 **读取配置**（如 `web.xml` 的 `<init-param>`）。
  - 用于 **数据库连接、资源初始化**。

代码示例

```java
@WebServlet(
        loadOnStartup = 1,
        urlPatterns = "/api/hello",
        initParams = {
            @WebInitParam(name = "username", value = "admin"),
            @WebInitParam(name = "password", value = "1234")
        }
)
public class HelloController extends HttpServlet {
    
    @Override
    public void init(ServletConfig config) throws ServletException {
        super.init(config);
        System.out.println(config.getInitParameter("username"));
        System.out.println(config.getInitParameter("password"));
        System.out.println("HelloController init 方法执行");
    }
    
}
```


**等效的 `web.xml` 配置（读取初始化参数）：**
```xml
<servlet>
    <servlet-name>HelloController</servlet-name>
    <servlet-class>com.demo.HelloController</servlet-class>
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


---

### **（3）请求处理（`service()`、`doGet()`、`doPost()` 方法）**
- **何时发生？**  
  - Servlet 初始化完成后，每当有 **客户端（浏览器）请求**，容器都会调用 `service()` 方法。
- **默认 `service()` 处理方式：**
  - `service()` **根据请求方法类型（GET、POST 等）分发到 `doGet()`、`doPost()` 等方法**。
  - 开发者一般**不重写 `service()`，而是重写 `doGet()`、`doPost()`**。

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
    response.getWriter().write("Hello, Servlet!");
}
```
**工作流程：**
```
客户端请求 (HTTP Request)
     │
     │
     ▼
容器调用 service()
     │
     ├── 如果是 GET 请求 → 调用 doGet()
     │
     ├── 如果是 POST 请求 → 调用 doPost()
     │
     └── 其他请求方式（如 PUT, DELETE） → 调用相应的 doPut()、doDelete()
```
**示例：**
```java
@Override
protected void doPost(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
    response.getWriter().write("处理 POST 请求");
}
```

---

### **（4）Servlet 销毁（`destroy()` 方法）**

- **服务器关闭** 或 **应用程序卸载** 时，容器会调用 `destroy()`。
- `destroy()` **只会被调用一次**，用于**释放资源**（如关闭数据库连接、释放线程池等）。


---

## **3. Servlet 生命周期示意图**
```plaintext
            ┌───────────────────────────┐
            │   Web 容器启动 (Tomcat)    │
            └───────────────────────────┘
                        │
                        ▼
            ┌──────────────────┐
            │  加载 Servlet 类  │
            └──────────────────┘
                        │
                        ▼
            ┌──────────────────┐
            │  实例化 Servlet   │
            └──────────────────┘
                        │
                        ▼
            ┌──────────────────┐
            │  调用 init() 方法 │
            └──────────────────┘
                        │
                        ▼
        ┌───────────────────────────┐
        │  处理请求 (service 方法)   │
        └───────────────────────────┘
            │       │       │
            ▼       ▼       ▼
         doGet()  doPost()  doPut() 等
                        │
                        ▼
        ┌───────────────────────────┐
        │  Web 容器关闭/应用卸载      │
        └───────────────────────────┘
                        │
                        ▼
            ┌──────────────────┐
            │  调用 destroy()   │
            └──────────────────┘
```

## 4.源码

`HelloController.java`
```java

@WebServlet(
        loadOnStartup = 1,
        urlPatterns = "/api/hello",
        initParams = {
            @WebInitParam(name = "username", value = "admin"),
            @WebInitParam(name = "password", value = "1234")
        }
)
public class HelloController extends HttpServlet {

    public HelloController() {
        System.out.println("HelloController 构造函数执行");
    }

    @Override
    public void init(ServletConfig config) throws ServletException {
        super.init(config);
        System.out.println(config.getInitParameter("username"));
        System.out.println(config.getInitParameter("password"));
        System.out.println("HelloController init 方法执行");
    }

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

    @Override
    public void destroy() {
        super.destroy();
        System.out.println("HelloController destroy 方法执行");

    }
}
```