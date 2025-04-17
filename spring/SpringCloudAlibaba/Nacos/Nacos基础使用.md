# Nacos基础使用


## 1. 启动/关闭 服务器

```bash

# 启动
sh startup.sh -m standalone

# 关闭
sh shutdown.sh
```

## 2. 验证

```bash

# 进入${nacos.home}/logs/ 目录下， 使用tail -f start.out 查看日志，
Nacos started successfully in stand alone mode. use embedded storage1
```

## 3. 服务注册

```bash

curl -X POST 'http://127.0.0.1:8848/nacos/v1/ns/instance?serviceName=nacos.naming.serviceName&ip=20.18.7.10&port=8080'
```

## 4. 服务发现

```bash

curl -X GET 'http://127.0.0.1:8848/nacos/v1/ns/instance/list?serviceName=nacos.naming.serviceName'
```

## 5. 发布配置

```bash

curl -X POST "http://127.0.0.1:8848/nacos/v1/cs/configs?dataId=nacos.cfg.dataId&group=test&content=HelloWorld"
```

## 6. 获取配置

```bash

curl -X GET "http://127.0.0.1:8848/nacos/v1/cs/configs?dataId=nacos.cfg.dataId&group=test"
```


## 7. 控制台页面

```text

http://127.0.0.1:8848/nacos
```