# Redis基础-Docker 单机安装

## 1. 下载镜像

```bash

docker pull redis:6.2.7
```

## 2. `compose.yml`

```yml

version: '3.9'
services:
  coms-redis6:
    image: redis:6.2.7
    container_name: redis6.coms.hello.com
    ports:
      - '6379:6379'
    volumes:
      - ./data:/data
      - ./conf:/data/conf:ro
    networks:
      - coms.hello.com
    command: ["redis-server" ,"/data/conf/redis.conf"]
networks:
  coms.hello.com:
    driver: bridge
    external:
      name: coms.hello.com

```

## 3. 配置

[配置参考](https://github.com/redis/redis/blob/unstable/redis.conf)