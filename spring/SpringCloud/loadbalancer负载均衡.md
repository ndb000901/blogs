# loadbalancer 负载均衡

## 1. 添加依赖

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

## 2. 使用 `LoadBalancerClient`

```java
@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    private DiscoveryClient discoveryClient;

    @Autowired
    private LoadBalancerClient loadBalancerClient;

    @Override
    public Order createOrder(Long userId, Long productId, Integer num) {

        // String service = getService();
        String service = getServiceByLoadBalancer();
        System.out.println(service);
        Product product = restTemplate.getForObject(service + "/product?id=" + productId + "&num=" + num, Product.class);
        if (product == null) {
            return null;
        }
        Order order = new Order();
        Long now = System.currentTimeMillis();
        order.setId(now);
        order.setAddress("address-" + now);
        order.setUserId(userId.toString());
        order.setTotalAmount(product.getPrice().multiply(new BigDecimal(product.getNum())));
        order.setNickName("nickName-" + now);
        List<Product> list = new ArrayList<>();
        list.add(product);
        order.setProductList(list);
        return order;
    }

    public String getService() {
        List<ServiceInstance> instances = discoveryClient.getInstances("svc-product");
        if (!instances.isEmpty()) {
            return instances.get(0).getUri().toString();
        }
        return null;
    }

    public String getServiceByLoadBalancer() {
        ServiceInstance instance = loadBalancerClient.choose("svc-product");
        return instance.getUri().toString();
    }

}
```

## 3. 使用`@LoadBalanced`注解

`OrderConfig.java`

```java

@Configuration
public class OrderConfig {


    @LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

`OrderServiceImpl.java`

使用服务名调用：`http://svc-product`

```java

@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    private DiscoveryClient discoveryClient;

    @Autowired
    private LoadBalancerClient loadBalancerClient;

    @Override
    public Order createOrder(Long userId, Long productId, Integer num) {

//        String service = getService();
//        String service = getServiceByLoadBalancer();
        // 使用服务名
        String service = "http://svc-product";
        System.out.println(service);
        Product product = restTemplate.getForObject(service + "/product?id=" + productId + "&num=" + num, Product.class);
        if (product == null) {
            return null;
        }
        Order order = new Order();
        Long now = System.currentTimeMillis();
        order.setId(now);
        order.setAddress("address-" + now);
        order.setUserId(userId.toString());
        order.setTotalAmount(product.getPrice().multiply(new BigDecimal(product.getNum())));
        order.setNickName("nickName-" + now);
        List<Product> list = new ArrayList<>();
        list.add(product);
        order.setProductList(list);
        return order;
    }

    public String getService() {
        List<ServiceInstance> instances = discoveryClient.getInstances("svc-product");
        if (!instances.isEmpty()) {
            return instances.get(0).getUri().toString();
        }
        return null;
    }

    public String getServiceByLoadBalancer() {
        ServiceInstance instance = loadBalancerClient.choose("svc-product");
        return instance.getUri().toString();
    }

}

```