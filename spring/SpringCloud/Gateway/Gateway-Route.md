# Gateway Route


**Route（路由）** 是 Gateway 最核心的构建块之一，定义了哪些请求如何被转发到下游服务。一个 Route 通常包括以下几个组成部分：

| 组件         | 说明 |
|--------------|------|
| `id`         | 路由唯一标识 |
| `predicates` | 断言，用于匹配请求 |
| `filters`    | 过滤器，对请求/响应做增强或修改 |
| `uri`        | 请求转发的目标地址 |
| `order`      | 路由顺序，值越小优先匹配 |

---

## 2. 示例（YAML 配置方式）

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order_route
          uri: http://localhost:8081
          predicates:
            - Path=/api/order/**
          filters:
            - StripPrefix=1
```

### 含义说明：

- `id`: 路由 ID
- `uri`: 请求转发的目标服务，可以是 `http://...` 或 `lb://服务名`
- `predicates`: 断言（请求匹配规则），如 Path、Header、Method 等
- `filters`: 请求/响应增强逻辑，如限流、鉴权、路径重写等

---

### URI 格式说明

- `http://localhost:8081` → 直接访问目标服务
- `lb://service-name` → 使用 Spring Cloud LoadBalancer 或 Nacos/Eureka 服务发现
- `forward:/internal` → 网关内部转发（不出网）

---

## 3. Java配置方式（替代 YAML）

```java
@Bean
public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
    return builder.routes()
        .route("order_route", r -> r.path("/api/order/**")
            .filters(f -> f.stripPrefix(1))
            .uri("lb://order-service"))
        .build();
}
```


