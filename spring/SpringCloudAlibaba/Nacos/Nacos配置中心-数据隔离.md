# Nacos数据中心-数据隔离


## 1. **使用命名空间（Namespace）隔离环境**


### 场景：
- **dev/test/prod** 环境配置隔离
- **不同租户或业务线** 配置隔离

### 做法：

1. 登录 Nacos 控制台，创建多个命名空间：
    - dev：开发环境
    - test：测试环境
    - prod：生产环境

2. Spring Boot 配置 application.yml 或 bootstrap.yml：
```yaml
spring:
  cloud:
    nacos:
      config:
        namespace: dev-namespace-id # 替换为对应命名空间的 ID
        server-addr: 127.0.0.1:8848
```

3. 配置文件放在对应的命名空间中：
    - `application-dev.yaml` → 放在 dev 命名空间
    - `application-prod.yaml` → 放在 prod 命名空间


---

## 2. **使用 Group 进行逻辑分组**

Nacos 配置默认使用 `DEFAULT_GROUP`，可以通过 Group 分组进一步组织配置。

### 场景：
- 多应用共用一个命名空间，但要区分业务线、模块或版本
- 如 `user-service` 和 `order-service` 各自的配置用不同 group

### 做法：

1. 设置 group：
```yaml
spring:
  cloud:
    nacos:
      config:
        group: USER_GROUP
```

2. 创建配置文件时选择对应 Group。


## 3. **使用 DataId 命名约定**

放服务的配置


