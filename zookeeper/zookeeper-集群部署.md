# zookeeper 集群部署

请在生产中使用集群部署，将节点分散到不同的机器上(不同的物理机)，至少3个节点。

我这里使用docker部署3个节点用于研究学习

## 1.下载镜像

```bash

docker pull zookeeper:3.9.3
```

## 2.创建目录

```bash

mkdir -p data1/data data1/log data2/data data2/log data3/data data3/log
```

### 目录结构

```text
├── compose.yml
├── data1
│   ├── data
│   └── log
├── data2
│   ├── data
│   └── log
└── data3
    ├── data
    └── log
```

## 2.配置文件

[配置详解](./zookeeper配置项详解.md)

节点配置 `zoo-.cfg`
```properties
tickTime=2000
dataDir=/data
clientPort=2181
admin.serverPort=8080
initLimit=5
syncLimit=2
server.1=coms.zookeeper.i1:2888:3888
server.2=coms.zookeeper.i2:2888:3888
server.3=coms.zookeeper.i3:2888:3888
```

`compose.yml`

```yml
name: coms-zookeeper # 项目名称
services:
  coms.zookeeper.i1:
    container_name: coms.zookeeper.i1 # 容器名称
    image: zookeeper:3.9.3 # 镜像
    build: . # 本地构建
    restart: unless-stopped # 重启
    environment:
      - ZOO_MY_ID=1
    ports: # 端口
      - "12181:2181"
      - "28081:8080"
    volumes: # 挂载目录
      - ./zoo.cfg:/conf/zoo.cfg:ro
      - ./data1/data:/data:rw
      - ./data1/log:/logs:rw
    networks:
      - coms.hello.com

  coms.zookeeper.i2:
    container_name: coms.zookeeper.i2 # 容器名称
    image: zookeeper:3.9.3 # 镜像
    build: . # 本地构建
    restart: unless-stopped # 重启
    environment:
      - ZOO_MY_ID=2
    ports: # 端口
      - "12182:2181"
      - "28082:8080"
    volumes: # 挂载目录
      - ./zoo.cfg:/conf/zoo.cfg:ro
      - ./data2/data:/data:rw
      - ./data2/log:/logs:rw
    networks:
      - coms.hello.com
  coms.zookeeper.i3:
    container_name: coms.zookeeper.i3 # 容器名称
    image: zookeeper:3.9.3 # 镜像
    restart: unless-stopped # 重启
    environment:
      - ZOO_MY_ID=3
    ports: # 端口
      - "12183:2181"
      - "28083:8080"
    volumes: # 挂载目录
      - ./zoo.cfg:/conf/zoo.cfg:ro
      - ./data3/data:/data:rw
      - ./data3/log:/logs:rw
    networks:
      - coms.hello.com
networks:
  coms.hello.com:
    driver: bridge
    external:
      name: coms.hello.com
```

## 3.启动集群

```bash

docker compose up -d
```

## 4.查看集群状态

```bash

docker compose ps
```

### 输出
```text
NAME                IMAGE             COMMAND                  SERVICE             CREATED       STATUS       PORTS
coms.zookeeper.i1   zookeeper:3.9.3   "/docker-entrypoint.…"   coms.zookeeper.i1   8 hours ago   Up 8 hours   2888/tcp, 3888/tcp, 0.0.0.0:12181->2181/tcp, :::12181->2181/tcp, 0.0.0.0:28081->8080/tcp, :::28081->8080/tcp
coms.zookeeper.i2   zookeeper:3.9.3   "/docker-entrypoint.…"   coms.zookeeper.i2   8 hours ago   Up 8 hours   2888/tcp, 3888/tcp, 0.0.0.0:12182->2181/tcp, :::12182->2181/tcp, 0.0.0.0:28082->8080/tcp, :::28082->8080/tcp
coms.zookeeper.i3   zookeeper:3.9.3   "/docker-entrypoint.…"   coms.zookeeper.i3   8 hours ago   Up 8 hours   2888/tcp, 3888/tcp, 0.0.0.0:12183->2181/tcp, :::12183->2181/tcp, 0.0.0.0:28083->8080/tcp, :::28083->8080/tcp
```

## 5.连接集群

```bash

docker exec -it coms.zookeeper.i1 zkCli.sh -server coms.zookeeper.i1:2181
```