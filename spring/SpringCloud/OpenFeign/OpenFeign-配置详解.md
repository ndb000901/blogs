# OpenFeign 配置详解

## 1. 配置

```yml
spring:
  cloud:
    openfeign:
      client:
        config:
          feignName:
            url: http://remote-service.com
            connectTimeout: 5000
            readTimeout: 5000
            loggerLevel: full
            errorDecoder: com.example.SimpleErrorDecoder
            retryer: com.example.SimpleRetryer
            defaultQueryParameters:
              query: queryValue
            defaultRequestHeaders:
              header: headerValue
            requestInterceptors:
              - com.example.FooRequestInterceptor
              - com.example.BarRequestInterceptor
            responseInterceptor: com.example.BazResponseInterceptor
            dismiss404: false
            encoder: com.example.SimpleEncoder
            decoder: com.example.SimpleDecoder
            contract: com.example.SimpleContract
            capabilities:
              - com.example.FooCapability
              - com.example.BarCapability
            queryMapEncoder: com.example.SimpleQueryMapEncoder
            micrometer.enabled: false
```



| 配置项                  | 作用 |
|-------------------------|------|
| `url`                   | 静态请求地址（绕过注册中心） |
| `connectTimeout`        | 连接超时 |
| `readTimeout`           | 读取超时 |
| `loggerLevel`           | 日志等级 |
| `errorDecoder`          | 自定义错误处理 |
| `retryer`               | 自定义重试策略 |
| `defaultQueryParameters`| 默认请求参数 |
| `defaultRequestHeaders` | 默认请求头 |
| `requestInterceptors`   | 自定义请求拦截器列表 |
| `responseInterceptor`   | 自定义响应拦截器（新） |
| `dismiss404`            | 是否忽略 404 |
| `encoder` / `decoder`   | 编码解码器 |
| `contract`              | 注解解析方式控制 |
| `capabilities`          | 自定义 SPI 插件机制 |
| `queryMapEncoder`       | Map 参数编码器 |
| `micrometer.enabled`    | 是否开启监控指标 |

