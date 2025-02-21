# Spring-Bean作用域

在 Spring 框架中，Bean 的作用域（Scopes）决定了 Bean 的生命周期和实例化策略。Spring 提供了几种不同的作用域，分别适用于不同的使用场景。以下是 Spring 6 中的常见 Bean Scope 详细介绍：

### 1. **Singleton Scope（单例）**
   - **默认作用域**：Spring 中的默认作用域是 `singleton`。
   - **生命周期**：在整个 Spring 容器生命周期内，Bean 只会有一个实例。无论容器中有多少个引用指向该 Bean，它们都会指向同一个实例。
   - **用途**：适用于无状态的 Bean，比如工具类、共享资源等。
   - **实现方式**：Spring 容器在启动时创建 Bean 的单个实例，并将其缓存以供后续调用。
   - **示例**：
     ```java
     @Component
     @Scope("singleton")
     public class MySingletonBean {
         // Bean定义
     }
     ```

### 2. **Prototype Scope（原型）**
   - **生命周期**：每次请求（例如通过 `getBean()`）都会创建一个新的实例。也就是说，每次访问该 Bean 时，Spring 都会返回一个新的 Bean 实例。
   - **用途**：适用于有状态的 Bean，或者需要每次都创建独立实例的场景。
   - **注意**：Spring 不会管理原型 Bean 的生命周期，意味着 Spring 容器不负责销毁该 Bean。因此，如果原型 Bean 中有资源需要清理（如关闭文件流等），你需要手动进行资源释放。
   - **示例**：
     ```java
     @Component
     @Scope("prototype")
     public class MyPrototypeBean {
         // Bean定义
     }
     ```

### 3. **Request Scope（请求）**
   - **生命周期**：对于每个 HTTP 请求，Spring 容器会为该请求创建一个新的 Bean 实例。当请求结束时，Bean 会被销毁。
   - **用途**：适用于 Web 应用中需要根据每个请求生成独立 Bean 的场景，比如会话信息、请求级别的数据等。
   - **示例**：
     ```java
     @Component
     @Scope("request")
     public class MyRequestScopedBean {
         // Bean定义
     }
     ```
   - **注意**：此作用域仅适用于基于 Servlet 的 Web 应用。

### 4. **Session Scope（会话）**
   - **生命周期**：每个用户的 HTTP 会话（Session）会创建一个新的 Bean 实例。这个 Bean 会在用户会话结束时销毁。
   - **用途**：适用于 Web 应用中需要在用户会话中共享数据的场景，如用户的登录信息、购物车等。
   - **示例**：
     ```java
     @Component
     @Scope("session")
     public class MySessionScopedBean {
         // Bean定义
     }
     ```

### 5. **Application Scope（应用）**
   - **生命周期**：一个 Bean 在整个应用上下文生命周期内是唯一的，并且它的生命周期等于 Spring 容器的生命周期。
   - **用途**：与 `singleton` 类似，但主要用于 Web 应用中的 ApplicationContext。
   - **示例**：
     ```java
     @Component
     @Scope("application")
     public class MyApplicationScopedBean {
         // Bean定义
     }
     ```

### 6. **WebSocket Scope（WebSocket）**
   - **生命周期**：该作用域是专门为 WebSocket 会话设计的，每个 WebSocket 会话有自己的 Bean 实例。
   - **用途**：适用于 WebSocket 应用中的场景。
   - **示例**：
     ```java
     @Component
     @Scope("websocket")
     public class MyWebSocketScopedBean {
         // Bean定义
     }
     ```

### 7. **Custom Scopes（自定义作用域）**
   - Spring 允许开发者创建自定义的作用域。这需要实现 `Scope` 接口并注册到 Spring 容器中。
   - **用途**：当内置的作用域不足以满足需求时，可以使用自定义作用域来满足特定的业务逻辑。
   - **示例**：
     ```java
     public class MyCustomScope implements Scope {
         @Override
         public Object get(String name, ObjectFactory<?> objectFactory) {
             // 自定义作用域的逻辑
             return objectFactory.getObject();
         }
         // 其他方法
     }
     ```

### 8. **作用域总结**
| 作用域       | 生命周期                                                                                           | 适用场景                                     |
|-------------|----------------------------------------------------------------------------------------------------|----------------------------------------------|
| `singleton` | 一个实例，应用上下文的生命周期内共享                                                                | 无状态 Bean，共享资源等                     |
| `prototype` | 每次请求都会创建一个新实例                                                                          | 有状态的 Bean，每次请求独立实例             |
| `request`   | 每个 HTTP 请求一个实例，随着请求结束而销毁                                                          | Web 应用中请求级别的数据                    |
| `session`   | 每个 HTTP 会话一个实例，随着会话结束而销毁                                                          | Web 应用中的用户会话数据                    |
| `application`| 应用上下文生命周期内共享的单例 Bean                                                                | Web 应用中的应用级数据                      |
| `websocket` | WebSocket 会话生命周期内共享的 Bean                                                                 | WebSocket 应用中的数据                      |
| 自定义作用域 | 根据需求实现自定义的作用域                                                                             | 特定业务场景                                 |

