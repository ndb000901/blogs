# OpenFeign-基础使用

## 1. 依赖

```yml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

## 2. 使用示例

`ProductClient.java`

```java

@FeignClient(value = "svc-product", fallbackFactory = ProductFallbackFactory.class)
public interface ProductClient {

    @RequestMapping(value = "/product", method = RequestMethod.GET)
    Product getProduct(@RequestParam("id") Long id, @RequestParam("num") Integer num);
}
```

开启 `@EnableFeignClients`
```java
@EnableDiscoveryClient
@SpringBootApplication
@EnableFeignClients
public class OrderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
}

```


## 3. 开启日志

```java

@Bean
public Logger.Level feignLoggerLevel() {
    return Logger.Level.FULL;
}
```

添加配置

```yml
logging:
  level:
    work.hello.svc.order.feign: debug
```

## 4. 配置

[配置详解](./OpenFeign-配置详解.md)


## 5. 拦截器

### 请求拦截器

`TokenInterceptor.java`

请求前添加 `x-token`请求头

```java

@Component
public class TokenInterceptor implements RequestInterceptor {

    @Override
    public void apply(RequestTemplate template) {
        template.removeHeader("x-token");
        template.header("x-token", UUID.randomUUID().toString());
    }
}
```

### 响应拦截器

`TagInterceptor.java`

添加响应头 `x-tag`

```java

@Component
public class TagInterceptor implements ResponseInterceptor {

    @Override
    public Object intercept(InvocationContext invocationContext, Chain chain) throws Exception {

        Collection<String> strings = invocationContext.response().headers().get("x-tag");

        if (strings != null) {
            System.out.println("tag: " + strings);
        }

        return chain.next(invocationContext);
    }
}
```


## 6. Fallback

### 添加依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

### 添加配置

```yml
feign:
  sentinel:
    enabled: true
```

### 方法1

```java

@Component
public class ProductFallback implements ProductClient {

    @Override
    public Product getProduct(Long id, Integer num) {
        Product product = new Product();
        product.setId(0L);
        product.setPrice(new BigDecimal(0));
        product.setProductName("other");
        product.setNum(0);
        return product;
    }
}
```
### 方法2, 可打印错误

```java
@Slf4j
@Component
public class ProductFallbackFactory implements FallbackFactory<ProductClient> {

    @Override
    public ProductClient create(Throwable cause) {
        log.error("fallback: ", cause);
        return (id, num) -> {
            Product product = new Product();
            product.setId(0L);
            product.setPrice(new BigDecimal(0));
            product.setProductName("other");
            product.setNum(0);
            return product;
        };

    }
}
```

### 使用

```java

//@FeignClient(value = "svc-product", fallback = ProductFallback.class)
@FeignClient(value = "svc-product", fallbackFactory = ProductFallbackFactory.class)
public interface ProductClient {

    @RequestMapping(value = "/product", method = RequestMethod.GET)
    Product getProduct(@RequestParam("id") Long id, @RequestParam("num") Integer num);
}
```

## 7. 重试

```java

@Bean
public Retryer retryer() {
    return new Retryer.Default();
}
```

Spring Cloud OpenFeign 默认的重试器是 Feign 的 `Retryer.Default`，它实现了一个 **指数退避 + 最大尝试次数限制** 的重试策略。


```java
public Default() {
    this(100, SECONDS.toMillis(1), 5);
}

public Default(long period, long maxPeriod, int maxAttempts) {

}
```

默认参数如下：

| 参数名       | 默认值        | 含义说明 |
|--------------|---------------|----------|
| period       | 100ms         | 初始重试间隔时间 |
| maxPeriod    | 1000ms（1秒） | 最大重试间隔时间 |
| maxAttempts  | 5             | 最多尝试次数（总共执行5次） |

---

### 计算间隔时间逻辑（指数退避）：

假设初始 `period = 100ms`：

| Attempt | 公式                          | Sleep 时间   |
|---------|-------------------------------|---------------|
| 1       | 100 × 1.5⁰ = 100              | 100ms         |
| 2       | 100 × 1.5¹ = 150              | 150ms         |
| 3       | 100 × 1.5² = 225              | 225ms         |
| 4       | 100 × 1.5³ = 337.5            | 337ms         |
| 5       | 100 × 1.5⁴ = 506.25           | 506ms         |
| 6       | 超出最大尝试次数，抛出异常     | 不再重试     |



### 自定义 Retryer 示例（只重试 3 次，间隔固定 200ms）：

```java
public class CustomRetryer implements Retryer {

    private int attempt = 1;
    private final int maxAttempts = 3;
    private final long waitTime = 200;

    @Override
    public void continueOrPropagate(RetryableException e) {
        if (attempt++ >= maxAttempts) {
            throw e;
        }
        try {
            Thread.sleep(waitTime);
        } catch (InterruptedException ignored) {
            Thread.currentThread().interrupt();
        }
    }

    @Override
    public Retryer clone() {
        return new CustomRetryer();
    }
}
```


