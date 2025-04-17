# Sentinel 使用教程

## 1. 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```


## 2. 配置

```yml
spring:
  cloud:
    sentinel:
      transport:
        port: 8719
        dashboard: localhost:8080

```

`spring.cloud.sentinel.transport.port` 端口配置会在应用对应的机器上启动一个 Http Server，该 Server 会与 Sentinel 控制台做交互。比如 Sentinel 控制台添加了 1 个限流规则，会把规则数据 push 给这个 Http Server 接收，Http Server 再将规则注册到 Sentinel 中

## 3. 资源限制

### 3.1 主流框架自动适配（Web Servlet、Dubbo、Spring Cloud、gRPC、Spring WebFlux、Reactor）；所有Web接口均为资源


### 3.2 `@SentinelResource` 注解

`SentinelDemo.java`

```java

@Slf4j
@Component
public class SentinelDemo {

    @SentinelResource(value = "sentinelResourceDemo", blockHandler = "sentinelResourceBlockHandler")
    public void sentinelResourceDemo() {
        log.info("sentinelResourceDemo run.....");
        sphUDemo();
    }

    public void sentinelResourceBlockHandler(BlockException ex) {
        log.error("sentinelResourceBlockHandler run...", ex);
    }

    public void sphUDemo() {
        try (Entry entry = SphU.entry("sphUDemo")) {
            // 受保护的业务逻辑
            log.info("order_create run.....");

        } catch (BlockException ex) {
            // 被限流/熔断
            log.warn("order_create 被限流了");
        }

    }
}
```

### 3.3 `ShpU` API

`SentinelDemo.java`

```java

@Slf4j
@Component
public class SentinelDemo {

    @SentinelResource(value = "sentinelResourceDemo", blockHandler = "sentinelResourceBlockHandler")
    public void sentinelResourceDemo() {
        log.info("sentinelResourceDemo run.....");
        sphUDemo();
    }

    public void sentinelResourceBlockHandler(BlockException ex) {
        log.error("sentinelResourceBlockHandler run...", ex);
    }

    public void sphUDemo() {
        try (Entry entry = SphU.entry("sphUDemo")) {
            // 受保护的业务逻辑
            log.info("order_create run.....");

        } catch (BlockException ex) {
            // 被限流/熔断
            log.warn("order_create 被限流了");
        }

    }
}
```

## 4. 异常处理


