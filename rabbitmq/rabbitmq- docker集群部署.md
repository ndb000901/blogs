# rabbitmq docker集群部署

## 1.下载镜像

```bash

docker pull rabbitmq:4.0.7
```

## 2.`compose.yml`

```yml

name: coms-rabbitmq # 项目名称
services:
  coms.rabbitmq.i1:
    container_name: coms.rabbitmq.i1 # 容器名称
    image: rabbitmq:4.0.7 # 镜像
    restart: unless-stopped # 重启
    ports: # 端口
      - "5672:5672"
      - "15672:15672"
    volumes: # 挂载目录
      - ./data1/etc/rabbitmq:/etc/rabbitmq:rw
      - ./data1/var/lib/rabbitmq:/var/lib/rabbitmq:rw
      - ./data1/var/log/rabbitmq:/var/log/rabbitmq:rw
    hostname: rabbitmq-i1
    networks:
      coms.hello.com: {}
  coms.rabbitmq.i2:
    container_name: coms.rabbitmq.i2 # 容器名称
    image: rabbitmq:4.0.7 # 镜像
    restart: unless-stopped # 重启
    ports: # 端口
      - "5673:5672"
      # - "15673:15672"
    volumes: # 挂载目录
      - ./data2/etc/rabbitmq:/etc/rabbitmq:rw
      - ./data2/var/lib/rabbitmq:/var/lib/rabbitmq:rw
      - ./data2/var/log/rabbitmq:/var/log/rabbitmq:rw
    hostname: rabbitmq-i2
    networks:
      coms.hello.com: {}
  coms.rabbitmq.i3:
    container_name: coms.rabbitmq.i3 # 容器名称
    image: rabbitmq:4.0.7 # 镜像
    restart: unless-stopped # 重启
    ports: # 端口
      - "5674:5672"
      # - "15674:15672"
    volumes: # 挂载目录
      - ./data3/etc/rabbitmq:/etc/rabbitmq:rw
      - ./data3/var/lib/rabbitmq:/var/lib/rabbitmq:rw
      - ./data3/var/log/rabbitmq:/var/log/rabbitmq:rw
    hostname: rabbitmq-i3
    networks:
      coms.hello.com: {}
networks:
  coms.hello.com:
    driver: bridge
    external:
      name: coms.hello.com
```

## 3.`rabbitmq.conf`

容器位置：`/etc/rabbitmq/rabbitmq.conf`

[参考](./rabbitmq-Docker部署.md)

## 4.`enabled_plugins` 启用插件

容器位置：`/etc/rabbitmq/enabled_plugins`

```text
[rabbitmq_federation_management,rabbitmq_management,rabbitmq_mqtt,rabbitmq_stomp].
```

## 5.`/var/lib/rabbitmq/.erlang.cookie`

容器位置: `/var/lib/rabbitmq/.erlang.cookie`

```bash
# 用于节点通信验证，随机字符串, 各个节点保持一致
xsaxsdxcsdcdcdcdscd
```

## 6.错误

[参考](./rabbitmq-Docker部署.md)


## 7.配置集群

```bash

#!/bin/bash


# node1
docker exec -it coms.rabbitmq.i1 rabbitmqctl stop_app
docker exec -it coms.rabbitmq.i1 rabbitmqctl reset
docker exec -it coms.rabbitmq.i1 rabbitmqctl start_app

# node2
docker exec -it coms.rabbitmq.i2 rabbitmqctl stop_app
docker exec -it coms.rabbitmq.i2 rabbitmqctl join_cluster rabbit@rabbitmq-i1
docker exec -it coms.rabbitmq.i2 rabbitmqctl start_app

# node3
docker exec -it coms.rabbitmq.i3 rabbitmqctl stop_app
docker exec -it coms.rabbitmq.i3 rabbitmqctl join_cluster rabbit@rabbitmq-i1
docker exec -it coms.rabbitmq.i3 rabbitmqctl start_app

```


## 8.创建用户

```bash
# docker exec coms.rabbitmq.i1 rabbitmqctl add_user hello hello123456

# 添加用户
rabbitmqctl add_user hello hello123456

# 修改密码
rabbitmqctl change_password hello new_password

# 设置用户权限
rabbitmqctl set_permissions -p / hello ".*" ".*" ".*"

# 设置为管理员（可登录 web 界面）
rabbitmqctl set_user_tags hello administrator

# 指定用户的权限
rabbitmqctl list_permissions -p /
```