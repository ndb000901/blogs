# Gateway Filter

在 Spring Cloud Gateway 中，**Filter（过滤器）** 是路由处理流程中的关键组件，作用是对请求和响应进行增强、修改或拦截处理。它可以实现认证、日志、限流、修改请求/响应、重定向等功能。

---

## 1. 分类

Spring Cloud Gateway 提供两种类型的过滤器：

| 类型              | 说明                                      |
|-------------------|-------------------------------------------|
| **GlobalFilter**  | 全局过滤器，作用于所有路由               |
| **GatewayFilter** | 路由级过滤器，仅作用于指定的路由         |

---

## 2. GlobalFilter（全局过滤器）

在 Spring Cloud Gateway 中，**内置全局过滤器（Global Filters）** 是对所有请求都生效的过滤器，属于整个网关生命周期的重要组成部分，执行优先级高于 Route 层级的局部 Filter。

---

| Bean 名称 / 类名 | 描述 |
|------------------|------|
| `NettyWriteResponseFilter` | **最后一个执行的 Filter**，用于将响应数据写入客户端。顺序：`WRITE_RESPONSE_FILTER_ORDER = -1` |
| `ForwardPathFilter` | 支持将请求 forward 到本地 controller。顺序：`FORWARD_PATH_FILTER_ORDER = 0` |
| `GatewayMetricsFilter` | 记录每个请求的指标（如耗时、状态码等），用于 Micrometer、Prometheus 监控。 |
| `RouteToRequestUrlFilter` | 根据 Route 中的 URI 构建实际的请求 URL（比如将 lb://svc 转换成 http://ip:port）。 |
| `LoadBalancerClientFilter` | 负责根据 serviceId 负载均衡选择服务实例，替换掉 URI。 |
| `WebsocketRoutingFilter` | 支持 WebSocket 转发。 |
| `NettyRoutingFilter` | 真正执行 HTTP 请求的过滤器（如果不是 WebSocket 请求）。 |
| `ForwardRoutingFilter` | 执行 forward 本地请求（比如 `/forward:/local/service`）。 |
| `GatewayMetricsFilter` | 收集请求耗时等指标信息。 |
| `RoutePredicateHandlerMapping` | Predicate 匹配入口 Filter，不算 GlobalFilter，但作用类似。 |

---

### 执行顺序说明（order）

Spring Cloud Gateway 中，全局过滤器的执行顺序由 `org.springframework.core.Ordered` 接口控制，数字越小优先级越高。

例如：
- `NettyRoutingFilter` 的顺序是 `HIGHEST_PRECEDENCE + 50`
- `NettyWriteResponseFilter` 的顺序是 `-1`（最后执行）

通过 `@Order` 注解或实现 `Ordered` 接口来自定义 Filter 顺序。

---

### 查看当前系统中所有 Global Filter 的方式

你可以在启动网关项目时设置日志级别：

```yaml
logging:
  level:
    org.springframework.cloud.gateway: DEBUG
```


## 3. 自定义全局过滤器


实现接口 `GlobalFilter` + `Ordered`，对所有请求生效。

### 重写请求路径

```java
@Component
public class ApiPrefixStripFilter implements GlobalFilter, Ordered {

    private static final String PREFIX = "/api";

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        ServerHttpRequest request = exchange.getRequest();
        String path = request.getURI().getRawPath();
        System.out.println("ApiPrefixStripFilter run .....: " + path);
        if (path.startsWith(PREFIX)) {
            String newPath = path.substring(PREFIX.length());
            ServerHttpRequest newRequest = request.mutate()
                    .path(newPath)
                    .build();
            ServerWebExchange newExchange = exchange.mutate()
                    .request(newRequest)
                    .build();
            return chain.filter(newExchange);
        }

        return chain.filter(exchange);
    }


    @Override
    public int getOrder() {
        // 确保在路由匹配前执行
        return -1;
    }
}
```

---

## 4. GatewayFilter（局部过滤器）


在 Spring Cloud Gateway 中，**局部过滤器（局部 Filter / Route Filter）** 是只作用于某个特定 Route 的过滤器，它们可以在配置文件中通过 `filters:` 配置进行使用。

---

### 内置局部过滤器列表

| Filter Factory 名称         | 描述 |
|-----------------------------|------|
| **AddRequestHeader**        | 添加请求头（Header） |
| **AddRequestParameter**     | 添加请求参数（Query Param） |
| **AddResponseHeader**       | 添加响应头 |
| **RemoveRequestHeader**     | 移除请求头 |
| **RemoveResponseHeader**    | 移除响应头 |
| **RewritePath**             | 重写请求路径 |
| **RedirectTo**              | 重定向到指定 URI |
| **SetPath**                 | 直接设置路径（替换路径） |
| **SetStatus**               | 设置响应状态码 |
| **PreserveHostHeader**      | 保留原始主机头 |
| **RequestRateLimiter**      | 请求限流（配合 Redis） |
| **Retry**                   | 请求重试 |
| **Hystrix**                 | 使用 Hystrix 进行熔断（老方案） |
| **CircuitBreaker**          | 熔断器（推荐，使用 Resilience4j） |
| **FallbackHeaders**         | 熔断 fallback 时添加额外 header |
| **PrefixPath**              | 给路径添加前缀 |
| **StripPrefix**             | 去除路径前缀 |
| **RequestSize**             | 限制请求体大小 |
| **SaveSession**             | 提前保存 Session（适用于 Redis 共享 Session） |
| **DedupeResponseHeader**    | 响应头去重 |
| **SetRequestHostHeader**    | 设置请求 Host 头 |
| **ModifyRequestBody**       | 修改请求体（需要编码器） |
| **ModifyResponseBody**      | 修改响应体 |
| **RequestHeaderToRequestUri** | 从 Header 设置请求 URL |
| **MapRequestHeader**        | 根据匹配规则对请求头进行映射替换 |
| **MapRequestBody**          | 修改请求体结构（类比 DTO 转换） |

---

### 示例用法（YAML 配置）

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: demo-route
          uri: http://localhost:8080
          predicates:
            - Path=/demo/**
          filters:
            - AddRequestHeader=X-Request-Foo, Bar
            - RewritePath=/demo/(?<segment>.*), /$\{segment}
            - StripPrefix=1
            - PrefixPath=/api
```

---



## 5. 自定义局部过滤器

`MyLogGatewayFilterFactory.java`

```java

@Component
public class MyLogGatewayFilterFactory extends AbstractGatewayFilterFactory<MyLogGatewayFilterFactory.Config> {
    public MyLogGatewayFilterFactory() {
        // 指定配置类
        super(Config.class); 
    }

    // 过滤器的执行逻辑
    @Override
    public GatewayFilter apply(Config config) {
        return (exchange, chain) -> {
            // 逻辑：打印请求日志
            ServerHttpRequest request = exchange.getRequest();
            System.out.println("[MyLogFilter] 请求路径: " + request.getPath());

            if (config.printParams) {
                request.getQueryParams().forEach((k, v) -> {
                    System.out.println("[MyLogFilter] 参数: " + k + "=" + v);
                });
            }

            // 继续过滤链
            return chain.filter(exchange);
        };
    }

    // 支持快捷配置字段顺序，例如 filters: - MyLog=true
    @Override
    public List<String> shortcutFieldOrder() {
        return Collections.singletonList("printParams");
    }

    // 配置类
    @Data
    public static class Config {
        private boolean printParams; // 是否打印参数
    }
}
```

### 配置

```yml
spring:
  application:
    name: gateway
  cloud:
    nacos:
      server-addr: 192.168.43.242:8848
    gateway:
      routes:
        - id: svc-order
          uri: lb://svc-order
          predicates:
            - Path=/api/order/**
            - MyUser=admin
          filters:
            - MyLog=true
        - id: svc-product
          uri: lb://svc-product
          predicates:
            - Path=/api/product/**
server:
  port: 38080
```

