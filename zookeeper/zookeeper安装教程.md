# zookeeper 安装教程

本文档用于学习zookeeper使用，生产环境请部署集群

## 1.下载

[下载页面](https://zookeeper.apache.org/releases.html)
```bash

wget -c https://www.apache.org/dyn/closer.lua/zookeeper/zookeeper-3.9.3/apache-zookeeper-3.9.3-bin.tar.gz
```

## 2.解压

```bash

tar -zxvf apache-zookeeper-3.9.3-bin.tar.gz
```

## 3.配置

新增 `conf/zoo.cfg` 文件

[配置解释](./zookeeper配置项详解.md)

```properties

# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/home/hello/local/zookeeper/apache-zookeeper-3.9.3-bin/data
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# https://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1

## Metrics Providers
#
# https://prometheus.io Metrics Exporter
#metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
#metricsProvider.httpHost=0.0.0.0
#metricsProvider.httpPort=7000
#metricsProvider.exportJvmInfo=true
```

## 4.启动

```bash

./bin/zkServer.sh start
```

### 输出
```text
ZooKeeper JMX enabled by default
Using config: /home/hello/local/zookeeper/apache-zookeeper-3.9.3-bin/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

## 5.查看状态

```bash

./bin/zkServer.sh status
```

### 输出
```text
ZooKeeper JMX enabled by default
Using config: /home/hello/local/zookeeper/apache-zookeeper-3.9.3-bin/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: standalone
```

## 6.关闭

```bash

./bin/zkServer.sh stop
```

### 输出
```text
ZooKeeper JMX enabled by default
Using config: /home/hello/local/zookeeper/apache-zookeeper-3.9.3-bin/bin/../conf/zoo.cfg
Stopping zookeeper ... STOPPED
```

## 7.支持的命令

```bash

./bin/zkServer.sh
```

### 输出
```text
ZooKeeper JMX enabled by default
Using config: /home/hello/local/zookeeper/apache-zookeeper-3.9.3-bin/bin/../conf/zoo.cfg
Usage: ./bin/zkServer.sh [--config <conf-dir>] {start|start-foreground|stop|version|restart|status|print-cmd}
```

## 8.连接到server

```bash


```