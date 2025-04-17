# Gateway 跨域配置

## 1. 全局跨域配置

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
            - MyUser=admin
          filters:
            - MyLog=true
        - id: svc-product
          uri: lb://svc-product
          predicates:
            - Path=/api/product/**
      globalcors:
        corsConfigurations:
          '[/**]': # 所有路径
#            allowed-origin-patterns:
#              - "*.hello.work"
            allowedOrigins:
              - "http://xx.hello.work"
            allowedMethods:
              - GET
              - POST
              - PUT
              - DELETE
              - OPTIONS
            allowedHeaders: "*"
            allowCredentials: true
            maxAge: 3600
server:
  port: 38080
```


## 2. 局部跨域配置

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
            - MyUser=admin
          filters:
            - MyLog=true
        - id: svc-product
          uri: lb://svc-product
          predicates:
            - Path=/api/product/**
          metadata:
            cors:
              allowedOrigins: '*'
              allowedMethods:
                - GET
                - POST
              allowedHeaders: '*'
              maxAge: 30
      globalcors:
        corsConfigurations:
          '[/**]': # 所有路径
#            allowed-origin-patterns:
#              - "*.hello.work"
            allowedOrigins:
              - "http://xx.hello.work"
            allowedMethods:
              - GET
              - POST
              - PUT
              - DELETE
              - OPTIONS
            allowedHeaders: "*"
            allowCredentials: true
            maxAge: 3600
server:
  port: 38080
```