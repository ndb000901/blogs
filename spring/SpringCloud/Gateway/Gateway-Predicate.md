# Gateway Predicate


## 1. 内置 Predicate 工厂

| Predicate 名称 | 示例 | 用途 |
|----------------|------|------|
| **After** | `- After=2024-12-31T23:59:59.000+08:00` | 请求时间在某时间**之后** |
| **Before** | `- Before=2024-01-01T00:00:00.000+08:00` | 请求时间在某时间**之前** |
| **Between** | `- Between=2024-01-01T00:00:00+08:00, 2024-12-31T23:59:59+08:00` | 请求时间在**时间区间内** |
| **Cookie** | `- Cookie=sessionId, \d+` | 按请求 Cookie 名和值（支持正则）匹配 |
| **Header** | `- Header=X-Token, .+` | 按请求 Header 键和值（支持正则）匹配 |
| **Host** | `- Host=**.example.com` | 按请求 Host（域名）匹配，支持通配符 |
| **Method** | `- Method=GET, POST` | 限定请求方法 |
| **Path** | `- Path=/api/user/**` | 匹配请求路径，支持 Ant 风格通配符 |
| **Query** | `- Query=foo, bar.*` | 匹配 URL 查询参数（可选正则） |
| **RemoteAddr** | `- RemoteAddr=192.168.1.1/24` | 匹配请求来源 IP（CIDR 格式） |
| **Weight** | `- Weight=group1, 80` | 权重路由（灰度发布、流量控制） |
| **CloudFoundryRouteService** | N/A | 专用于 Cloud Foundry Route Service，较少用 |

---

### After / Before / Between
- 时间格式必须是 ISO-8601（含时区）
- 常用于临时开关、活动控制等场景

### Cookie / Header / Query
- 第二个参数是可选的**正则表达式**
- 若只写一个值，则匹配是否存在此参数/头/cookie

### Host
- 支持 `*`（匹配一个 path 片段）和 `**`（匹配多个片段）

### Path
- 最常用 Predicate，用于路由匹配
- 如：`/api/order/**` 会匹配 `/api/order/1` 等

### Method
- 支持多个 HTTP 方法，以逗号分隔
- 必须是大写，如 GET、POST、PUT

### RemoteAddr
- 支持 IPv4 和 IPv6 CIDR 表达式
- 用于白名单控制、内部接口限制等

### Weight
- 用于 **灰度发布**，多个路由属于同一 group，共享一个总权重

---

## 2. 多 Predicate 同时使用（**AND 关系**）

```yaml
predicates:
  - Path=/api/order/**
  - Method=GET
  - Header=X-User, \d+
```

上述配置表示必须满足路径匹配 + 方法匹配 + Header 匹配，才会命中该路由。

---

## 3. 自定义Predicate


### 创建 Predicate 工厂类

必须继承 `AbstractRoutePredicateFactory<T>` 类，并命名为 `XXXRoutePredicateFactory`，Spring Cloud Gateway 会自动识别。

`MyUserRoutePredicateFactory.java`

```java
@Component
public class MyUserRoutePredicateFactory extends AbstractRoutePredicateFactory<MyUserRoutePredicateFactory.Config> {

    public MyUserRoutePredicateFactory() {
        super(Config.class);
    }

    @Override
    public Predicate<ServerWebExchange> apply(Config config) {

        return exchange -> {

            String username = exchange.getRequest().getQueryParams().getFirst("user");
            return username != null && username.equals(config.username);
        };
    }

    @Override
    public List<String> shortcutFieldOrder() {
        return Collections.singletonList("username");
    }

    @Data
    public static class Config {
        private String username;
    }
}
```

> 上面这个自定义断言的意思是：当请求参数中 `user=xxx` 且等于配置中的 `username` 时才路由通过。

---

### 在配置文件中使用

```yaml

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
        - id: svc-product
          uri: lb://svc-product
          predicates:
            - Path=/api/product/**
server:
  port: 38080
```

---


