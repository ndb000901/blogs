# Sentinel 异常处理

## 1. `@SentinelResource` 异常处理


`@SentinelResource` 提供了以下 4 个重要属性，用于处理异常与兜底逻辑：

| 属性名 | 描述 |
|--------|------|
| `value` | 资源名，必须填 |
| `blockHandler` | 限流/降级等 Sentinel 抛出异常（BlockException）的处理方法 |
| `fallback` | 业务异常（如 NullPointerException）时的兜底处理方法 |
| `exceptionsToIgnore` | 哪些异常不进入 fallback，而是直接抛出 |

---

### 1.1 限流兜底：`blockHandler`

```java
@SentinelResource(value = "orderCreate", blockHandler = "handleBlock")
public String createOrder() {
    // 模拟调用限流资源
    return "order created";
}

public String handleBlock(BlockException ex) {
    log.warn("被限流啦：{}", ex.toString());
    return "fallback: 限流了";
}
```

当 `orderCreate` 被 Sentinel 限流时，会进入 `handleBlock()` 方法处理。

---

### 1.2 异常兜底：`fallback`

```java
@SentinelResource(value = "pay", fallback = "payFallback")
public String pay(Integer id) {
    if (id == null) throw new IllegalArgumentException("id 不能为 null");
    return "支付成功";
}

public String payFallback(Integer id, Throwable ex) {
    log.error("业务异常，回退：{}", ex.getMessage());
    return "系统异常，请稍后再试";
}
```

当 `pay` 中抛出业务异常（如 NPE、IAE），会进入 `payFallback()` 方法处理。

---

### 1.3 同时使用 blockHandler + fallback

```java
@SentinelResource(value = "query", blockHandler = "handleBlock", fallback = "handleFallback")
public String query(Integer id) {
    if (id < 0) throw new RuntimeException("非法请求");
    return "query ok";
}

public String handleBlock(Integer id, BlockException ex) {
    return "被限流了";
}

public String handleFallback(Integer id, Throwable ex) {
    return "业务异常：" + ex.getMessage();
}
```

> 注意：两个兜底方法的参数签名都要匹配（参数 + 异常），方法必须是 **public**, 不能和主方法在不同类（除非加上 `fallbackClass` 和 `blockHandlerClass`）

---

### 1.4 忽略某些异常：`exceptionsToIgnore`

```java
@SentinelResource(
    value = "checkUser",
    fallback = "userFallback",
    exceptionsToIgnore = {IllegalArgumentException.class}
)
public String checkUser(String name) {
    if (name == null) throw new NullPointerException("空指针");
    if ("admin".equals(name)) throw new IllegalArgumentException("非法用户");
    return "ok";
}

public String userFallback(String name, Throwable ex) {
    return "系统异常，请稍后再试：" + ex.getMessage();
}
```

> 上例中，如果传入 "admin"，抛出 `IllegalArgumentException`，**不会进入 fallback**，而是直接抛出异常。

---

## 2.Web接口资源的异常处理

### 2.1 接口异常处理

```java

import com.alibaba.csp.sentinel.adapter.spring.webmvc_v6x.callback.BlockExceptionHandler;
import com.alibaba.csp.sentinel.slots.block.BlockException;
import com.alibaba.csp.sentinel.slots.block.authority.AuthorityException;
import com.alibaba.csp.sentinel.slots.block.degrade.DegradeException;
import com.alibaba.csp.sentinel.slots.block.flow.FlowException;
import com.alibaba.csp.sentinel.slots.block.flow.param.ParamFlowException;
import com.alibaba.csp.sentinel.slots.system.SystemBlockException;
import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;

import java.io.IOException;
import java.util.Map;

@Configuration
public class SentinelConfig {

    @Bean
    public BlockExceptionHandler blockExceptionHandler() {
        return new BlockExceptionHandler() {

            @Override
            public void handle(HttpServletRequest request, HttpServletResponse response, String s, BlockException e) throws IOException {
                response.setStatus(HttpStatus.TOO_MANY_REQUESTS.value());
                response.setCharacterEncoding("UTF-8");
                response.setContentType(MediaType.APPLICATION_JSON_VALUE);

                String msg = "请求被限流了";
                if (e instanceof FlowException) {
                    msg = "请求被限流了";
                } else if (e instanceof DegradeException) {
                    msg = "服务被降级了";
                } else if (e instanceof ParamFlowException) {
                    msg = "热点参数限流";
                } else if (e instanceof SystemBlockException) {
                    msg = "系统保护规则触发";
                } else if (e instanceof AuthorityException) {
                    msg = "授权规则不通过";
                }

                String json = new ObjectMapper().writeValueAsString(Map.of(
                    "code", 429,
                    "message", msg
                ));
                response.getWriter().write(json);
            }
        };
    }
}
```

| 异常类 | 含义 |
|--------|------|
| `FlowException` | 限流 |
| `DegradeException` | 熔断降级 |
| `ParamFlowException` | 热点参数限流 |
| `SystemBlockException` | 系统保护（如 load、RT） |
| `AuthorityException` | 授权规则不通过 |



### 2.2 Gateway 处理异常

Spring Cloud Gateway 配合 Sentinel 时，不使用 `BlockExceptionHandler`，而是配置全局的 `SentinelGatewayBlockExceptionHandler`，可通过以下方式重写：

```java
@Configuration
public class GatewayBlockHandlerConfig {

    @PostConstruct
    public void init() {
        GatewayCallbackManager.setBlockHandler((exchange, t) -> {
            ServerHttpResponse response = exchange.getResponse();
            response.setStatusCode(HttpStatus.TOO_MANY_REQUESTS);
            response.getHeaders().setContentType(MediaType.APPLICATION_JSON);

            String body = "{\"code\":429,\"msg\":\"接口被限流啦\"}";
            DataBuffer buffer = response.bufferFactory().wrap(body.getBytes(StandardCharsets.UTF_8));
            return response.writeWith(Mono.just(buffer));
        });
    }
}
```

---

#### 注意点

1. **自定义异常处理器必须是 Spring 容器的 Bean**
2. **不要和 Spring Boot 自带的错误页面混淆**，Sentinel 的是规则触发而非系统异常
3. **控制返回的 HTTP 状态码** 是对接前端的重要细节
4. **可以结合 SLF4J 日志打印具体的拦截类型**，便于排查限流情况

---




## 3. `OpenFeign` 调用的异常处理

由fallback兜底

```java

//@FeignClient(value = "svc-product", fallback = ProductFallback.class)
@FeignClient(value = "svc-product", fallbackFactory = ProductFallbackFactory.class)
public interface ProductClient {

    @RequestMapping(value = "/product", method = RequestMethod.GET)
    Product getProduct(@RequestParam("id") Long id, @RequestParam("num") Integer num);
}
```


## 4. `SphU` 异常处理

try catch 自行处理
```java

    public void sphUDemo() {
        try (Entry entry = SphU.entry("sphUDemo")) {
            // 受保护的业务逻辑
            log.info("order_create run.....");

        } catch (BlockException ex) {
            // 被限流/熔断
            log.warn("order_create 被限流了");
        }

    }
```