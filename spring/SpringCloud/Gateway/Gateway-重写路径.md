# Gateway 重写路径

网关接口: `/api/order/xxxxx`

order服务接口: `/order/xxxxx`


## 1. `RewritePath` 过滤器处理

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
          filters:
            - RewritePath=/api/(?<segment>.*), /$\{segment}
        - id: svc-product
          uri: lb://svc-product
          predicates:
            - Path=/api/product/**
          filters:
            - RewritePath=/api/(?<segment>.*), /$\{segment}
server:
  port: 38080
```


## 2. 使用 全局过滤器 `GlobalFilter` 实现统一前缀重写

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
