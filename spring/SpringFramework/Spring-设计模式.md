# Spring 设计模式

## 1. **工厂模式（Factory Pattern）**

### 应用：
- **`BeanFactory`** 和 **`ApplicationContext`**
- Spring 用这些工厂负责创建和管理 Bean 对象。

### 作用：
- 解耦对象的创建过程。
- 实现依赖注入的核心机制。

---

## 2. **单例模式（Singleton Pattern）**

### 应用：
- 默认情况下，Spring 中的 Bean 是单例的。

### 作用：
- 节省资源，提升性能。
- 通过容器统一管理单例 Bean。

---

## 3. **代理模式（Proxy Pattern）**

### 应用：
- **AOP（面向切面编程）**
    - 基于 JDK 动态代理 或 CGLIB 字节码生成
- **事务管理、权限控制、日志记录**

### 作用：
- 在不改变原始对象代码的情况下增强其功能。

---

## 4. **模板方法模式（Template Method Pattern）**

### 应用：
- **`JdbcTemplate`**、`RestTemplate`、`RedisTemplate`
- 统一了核心操作流程，提供扩展点。

### 作用：
- 提取算法骨架，让子类实现扩展点。
- 简化常用操作，避免重复代码。

---

## 5. **策略模式（Strategy Pattern）**

### 应用：
- **`Resource` 接口及其多种实现类（ClassPathResource、FileSystemResource 等）**
- **Spring Security** 中的认证策略、密码策略、访问控制策略等

### 作用：
- 运行时选择不同的行为或策略。
- 提高系统灵活性和可扩展性。

---

## 6. **观察者模式（Observer Pattern）**

### 应用：
- Spring 事件发布机制：
    - `ApplicationEventPublisher` + `@EventListener`

### 作用：
- 解耦事件源与监听器。
- 实现发布/订阅模型。

---

## 7. **装饰器模式（Decorator Pattern）**

### 应用：
- **`BeanPostProcessor` / `BeanFactoryPostProcessor`**
    - 对 Bean 实例进行“装饰”增强。
- AOP 本质上也是某种装饰器机制的体现。

---

## 8. **适配器模式（Adapter Pattern）**

### 应用：
- **HandlerAdapter** 适配不同类型的 Controller（Spring MVC）
- **`WebMvcConfigurer`** 接口

### 作用：
- 使接口兼容，提升灵活性。

---

## 9. **责任链模式（Chain of Responsibility）**

### 应用：
- **Spring Security 的 FilterChain**
- **Servlet FilterChain、HandlerInterceptor**

### 作用：
- 动态构建处理链，职责单一、解耦灵活。

---

## 10. **建造者模式（Builder Pattern）**

### 应用：
- 用于构建复杂 Bean 的配置对象，例如：
    - `BeanDefinitionBuilder`
    - `SpringApplicationBuilder`

---

## 11. **享元模式（Flyweight Pattern）**

### 应用：
- Bean 缓存、Scope 管理（如单例复用）
- 属性值共享、字符串池等轻量化场景

---

## 12. **原型模式（Prototype Pattern）**

### 应用：
- Spring 的 Bean 作用域为 `prototype` 时，每次获取都会新建一个实例。

---

## 13. 补充（扩展类设计模式）

- **合成模式（Composite Pattern）**：如 BeanFactory 层级组合。
- **桥接模式（Bridge Pattern）**：如资源加载（Resource + ResourceLoader 解耦）。

---

