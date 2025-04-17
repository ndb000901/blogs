# Nacos 服务注册/发现

## 1.依赖

```yml

<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

## 2. 配置

```properties

spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
```

## 3. `@EnableDiscoveryClient` 注解开启服务注册与发现

```java

@EnableDiscoveryClient
@SpringBootApplication
public class OrderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
}
```

## 4. 注册发现示例

```java


@SpringBootTest
public class TestServiceDiscovery {

    @Autowired
    private DiscoveryClient discoveryClient;

    @Autowired
    private NacosServiceDiscovery nacosServiceDiscovery;


    @Test
    public void testDiscovery() {

        List<String> services = discoveryClient.getServices();
        for (String service : services) {
            List<ServiceInstance> instances = discoveryClient.getInstances(service);
            for (ServiceInstance instance : instances) {
                System.out.println("instance: " + instance.getInstanceId());
            }
        }
    }

    @Test
    public void testNacosServiceDiscovery() throws NacosException {
        List<String> services = nacosServiceDiscovery.getServices();
        for (String service : services) {
            List<ServiceInstance> instances = nacosServiceDiscovery.getInstances(service);
            for (ServiceInstance instance : instances) {
                System.out.println("instance: " + instance.getInstanceId());
            }
        }
    }
}
```