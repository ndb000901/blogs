# RestTemplate 使用

## 1. 添加示例

```java
@Configuration
public class OrderConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

```

## 2. 使用示例

```java
@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    private DiscoveryClient discoveryClient;

    @Override
    public Order createOrder(Long userId, Long productId, Integer num) {

        String service = getService();
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

}
```