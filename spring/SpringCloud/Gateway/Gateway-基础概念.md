# Gateway 基础概念

Spring Cloud Gateway 是 Spring Cloud 官方推出的一款 **高性能、基于响应式编程模型的 API 网关框架**，用于替代 Netflix Zuul。



## 1. 核心设计

Spring Cloud Gateway 的架构可以分为三层：

```
           ┌──────────────┐
           │  客户端请求    │
           └──────┬───────┘
                  ▼
           ┌──────────────┐
           │  路由匹配层    │  Route Predicate
           └──────┬───────┘
                  ▼
           ┌──────────────┐
           │  过滤器链层    │  Global/Route Filter Chain
           └──────┬───────┘
                  ▼
           ┌──────────────┐
           │  目标服务转发  │  LoadBalancer/URL
           └──────────────┘
```

---

## 2. 核心组件设计

### 2.1 **Route（路由）**
- 一个 `Route` 定义了一个匹配规则（Predicate）和目标服务地址
- 结构包括：
    - `id`
    - `uri`：目标地址（可为 lb://svc-name）
    - `predicates`：请求匹配规则（如 path、method、host、header）
    - `filters`：前置或后置逻辑处理

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: product_route
          uri: lb://svc-product
          predicates:
            - Path=/product/**
```

---

### 2.2 **Predicate（断言）**
- 决定请求是否匹配某个 Route
- 常见 Predicate：
    - Path
    - Method
    - Header
    - Host
    - Query

例如：
```yaml
predicates:
  - Path=/api/**
  - Method=GET
```

---

### 2.3 **Filter（过滤器）**
- 请求或响应处理逻辑（如鉴权、日志、限流、重写路径等）
- 分为：
    - **Global Filter**：所有路由共享
    - **Route Filter**：单个路由私有
- 内置过滤器如：
    - `AddRequestHeader`
    - `StripPrefix`
    - `RequestRateLimiter`（基于 Redis 限流）

---

### 2.4 **GatewayFilterChain**
- Filter 的执行链（责任链模式）
- 按顺序执行，每个过滤器可以决定是否继续传递请求

---

## 3. 请求处理流程

1. 客户端发起请求
2. 匹配 Predicate（路径、方法等）
3. 构造 GatewayFilterChain
4. 执行过滤器链
5. 路由请求到目标服务
6. 返回响应并经过过滤器链处理

---


## 4. 对比 Zuul 

| 特性 | Zuul (1.x) | Spring Cloud Gateway |
|------|------------|----------------------|
| 架构 | 阻塞 Servlet | 非阻塞 WebFlux |
| 性能 | 一般        | 更高并发能力 |
| 扩展性 | 低        | 高（工厂模式） |
| 动态路由 | 不便捷     | 非常简单 |
| 支持断言/过滤器 | 少 | 多且可组合 |
| 限流支持 | 手动实现   | 内置支持 |

---

