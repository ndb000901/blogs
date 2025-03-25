# zookeeper-启用jmx

## 1.docker-compose配置

[官方文档](https://zookeeper.apache.org/doc/r3.9.3/zookeeperJMX.html)

加入启动参数

[参考配置](./zookeeper-集群部署.md)

```properties
JVMFLAGS=-Xmx10240m -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=1099 -Dcom.sun.management.jmxremote.rmi.port=11198 -Dcom.sun.management.jmxremote.local.only=false -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Djava.rmi.server.hostname=192.168.43.242
```


## 2.单机

```bash

# 在zkServer.sh中加入
export JVMFLAGS="-Xmx10240m \
  -Dcom.sun.management.jmxremote \
  -Dcom.sun.management.jmxremote.port=1099 \
  -Dcom.sun.management.jmxremote.rmi.port=11098 \
  -Dcom.sun.management.jmxremote.local.only=false \
  -Dcom.sun.management.jmxremote.authenticate=false \
  -Dcom.sun.management.jmxremote.ssl=false \
  -Djava.rmi.server.hostname=192.168.43.242"

```

参数解释
- `-Dcom.sun.management.jmxremote`：启用 JMX 远程监控。

- `-Dcom.sun.management.jmxremote.port=1099`：设置 JMX 端口为 1099。

- `-Dcom.sun.management.jmxremote.rmi.port=11299`：设置 RMI 端口为 11098。

- `-Dcom.sun.management.jmxremote.local.only=false`：允许远程连接。

- `-Dcom.sun.management.jmxremote.authenticate=false`：禁用身份验证。

- `-Dcom.sun.management.jmxremote.ssl=false`：禁用 SSL。

- `-Djava.rmi.server.hostname=192.168.43.242`：设置RMI hostname。


