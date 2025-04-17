# Nacos配置中心接入

## 1. 依赖

```yml

<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

## 2. 配置

`application.yml`

```yml
spring:
  application:
    name: svc-order
  profiles:
    active: dev
  cloud:
    nacos:
      server-addr: 192.168.43.242:8848
      config:
        namespace: ${spring.profiles.active:dev}
  config:
    import: nacos:common.yml?group=svc-order
server:
  port: 31100
```

`common.yml`

```yml
order:
  config:
    name: dev-name
    maxNum: 111
```

可跳过nacos配置检查，当没有import时：`spring.cloud.nacos.config.import-check.enabled=false`

## 3. 使用 `@ConfigurationProperties`读取

当配置中心数据变更后会自动刷新

```java

@Component
@ConfigurationProperties(prefix = "order.config")
@Data
public class OrderPropertirs {

    private String name;

    private int maxNum;
}
```

## 4. 使用`@Value` 读取

`@Value` 可使配置中心数据发送变更时自动刷新

```java

@RefreshScope
@RestController
public class ConfigController {


    @Value("${order.config.name}")
    private String name;

    @Value("${order.config.maxNum}")
    private int maxNum;

    @Autowired
    private OrderPropertirs orderPropertirs;

    @GetMapping("/order/config")
    public Map<String, Object> getConfig() {
        Map<String, Object> result = new HashMap<>();
        result.put("name", name);
        result.put("maxNum", String.valueOf(maxNum));
        result.put("config", orderPropertirs);
        return result;
    }
}

```

## 5. 可添加监听器，当配置发生变更时触发

```java
@Slf4j
@Component
public class ConfigListener {

    public static final String DATA_ID = "svc-order.properties";

    public static final String GROUP = "DEFAULT_GROUP";

    @Autowired
    private NacosConfigManager nacosConfigManager;

    @PostConstruct
    public void init() throws NacosException {
        ConfigService configService = nacosConfigManager.getConfigService();
        configService.addListener(DATA_ID, GROUP, new Listener() {
            @Override
            public Executor getExecutor() {
                return Executors.newSingleThreadExecutor();
            }

            @Override
            public void receiveConfigInfo(String configInfo) {
                log.info("[dataId]:[" + DATA_ID + "],Configuration changed to:"
						+ configInfo);
            }
        });

    }
}
```
