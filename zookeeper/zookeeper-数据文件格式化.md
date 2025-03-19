# zookeeper-数据文件格式化

## 1.事务日志文件


```bash

java -cp "./zookeeper-3.9.3.jar:./*" org.apache.zookeeper.server.persistence.TxnLogToolkit /home/hello/docker/zookeeper/data1/data/version-2/log.200000001

./zkTxnLogToolkit.sh /home/hello/docker/zookeeper/data1/data/version-2/log.200000001

```

### 输出

```text

ZooKeeper Transactional Log File with dbid 0 txnlog format version 2
13:42:21.551 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -
13:42:21.554 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -   ______                  _
13:42:21.555 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -  |___  /                 | |
13:42:21.555 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -     / /    ___     ___   | | __   ___    ___   _ __     ___   _ __
13:42:21.555 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -    / /    / _ \   / _ \  | |/ /  / _ \  / _ \ | '_ \   / _ \ | '__|
13:42:21.555 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -   / /__  | (_) | | (_) | |   <  |  __/ |  __/ | |_) | |  __/ | |
13:42:21.555 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -  /_____|  \___/   \___/  |_|\_\  \___|  \___| | .__/   \___| |_|
13:42:21.555 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -                                               | |
13:42:21.555 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -                                               |_|
13:42:21.556 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:zookeeper.version=3.9.3-c26634f34490bb0ea7a09cc51e05ede3b4e320ee, built on 2024-10-17 23:21 UTC
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:host.name=dev
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.version=17.0.14
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.vendor=Oracle Corporation
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.home=/home/hello/local/java/jdk-17.0.14
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.class.path=./zookeeper-3.9.3.jar:./jetty-util-9.4.56.v20240826.jar:./jetty-util-ajax-9.4.56.v20240826.jar:./zookeeper-jute-3.9.3.jar:./netty-codec-4.1.113.Final.jar:./metrics-core-4.1.12.1.jar:./netty-transport-classes-epoll-4.1.113.Final.jar:./netty-buffer-4.1.113.Final.jar:./netty-transport-native-epoll-4.1.113.Final-linux-x86_64.jar:./logback-core-1.2.13.jar:./audience-annotations-0.12.0.jar:./jetty-server-9.4.56.v20240826.jar:./netty-transport-native-unix-common-4.1.113.Final.jar:./netty-tcnative-boringssl-static-2.0.66.Final-windows-x86_64.jar:./jetty-servlet-9.4.56.v20240826.jar:./simpleclient_common-0.9.0.jar:./slf4j-api-1.7.30.jar:./netty-tcnative-classes-2.0.66.Final.jar:./jetty-io-9.4.56.v20240826.jar:./jetty-http-9.4.56.v20240826.jar:./jline-2.14.6.jar:./zookeeper-prometheus-metrics-3.9.3.jar:./javax.servlet-api-3.1.0.jar:./netty-tcnative-boringssl-static-2.0.66.Final-linux-aarch_64.jar:./netty-tcnative-boringssl-static-2.0.66.Final-linux-x86_64.jar:./zookeeper-3.9.3.jar:./simpleclient_servlet-0.9.0.jar:./jackson-core-2.15.2.jar:./jetty-security-9.4.56.v20240826.jar:./netty-tcnative-boringssl-static-2.0.66.Final-osx-aarch_64.jar:./logback-classic-1.2.13.jar:./snappy-java-1.1.10.5.jar:./commons-io-2.17.0.jar:./jackson-annotations-2.15.2.jar:./netty-tcnative-boringssl-static-2.0.66.Final.jar:./commons-cli-1.5.0.jar:./simpleclient_hotspot-0.9.0.jar:./simpleclient-0.9.0.jar:./netty-handler-4.1.113.Final.jar:./netty-transport-4.1.113.Final.jar:./netty-tcnative-boringssl-static-2.0.66.Final-osx-x86_64.jar:./netty-resolver-4.1.113.Final.jar:./jackson-databind-2.15.2.jar:./netty-common-4.1.113.Final.jar
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.library.path=/usr/java/packages/lib:/usr/lib64:/lib64:/lib:/usr/lib
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.io.tmpdir=/tmp
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.compiler=<NA>
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.name=Linux
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.arch=amd64
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.version=5.15.0-134-generic
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:user.name=hello
13:42:21.563 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:user.home=/home/hello
13:42:21.564 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:user.dir=/home/hello/local/zookeeper/apache-zookeeper-3.9.3-bin/lib
13:42:21.564 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.memory.free=501MB
13:42:21.564 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.memory.max=8016MB
13:42:21.564 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.memory.total=512MB
13:42:21.564 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.enableEagerACLCheck = false
13:42:21.565 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.digest.enabled = true
13:42:21.565 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.closeSessionTxn.enabled = true
13:42:21.565 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.serializeLastProcessedZxid.enabled = true
13:42:21.566 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.flushDelay = 0 ms
13:42:21.566 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.maxWriteQueuePollTime = 0 ms
13:42:21.566 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.maxBatchSize=1000
13:42:21.566 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.intBufferStartingSizeBytes = 1024
3/17/25, 2:00:39 AM UTC session 0x1000000b39e0000 cxid 0x0 zxid 0x200000001 createSession 30000
3/17/25, 1:41:06 PM UTC session 0x1000000b39e0001 cxid 0x0 zxid 0x200000002 createSession 30000
3/17/25, 1:41:25 PM UTC session 0x1000000b39e0001 cxid 0x3 zxid 0x200000003 create /hello,,[31,s{'world,'anyone}
],false,1
3/17/25, 1:41:45 PM UTC session 0x1000000b39e0001 cxid 0x7 zxid 0x200000004 error -101
3/17/25, 1:41:47 PM UTC session 0x1000000b39e0001 cxid 0x8 zxid 0x200000005 create /hello/lock,,[31,s{'world,'anyone}
],false,1
3/17/25, 1:41:49 PM UTC session 0x1000000b39e0001 cxid 0x9 zxid 0x200000006 create /hello/lock/s,,[31,s{'world,'anyone}
],false,1
3/17/25, 1:41:58 PM UTC session 0x1000000b39e0001 cxid 0xa zxid 0x200000007 create /hello/lock/s/r0000000000,,[31,s{'world,'anyone}
],true,1
3/17/25, 1:42:04 PM UTC session 0x1000000b39e0001 cxid 0xb zxid 0x200000008 create /hello/lock/s/r-0000000001,,[31,s{'world,'anyone}
],true,2
3/17/25, 1:42:22 PM UTC session 0x1000000b39e0001 cxid 0x10 zxid 0x200000009 create /hello/lock/s/w-0000000002,,[31,s{'world,'anyone}
],true,3
3/19/25, 12:30:30 PM UTC session 0x1000000b39e0002 cxid 0x0 zxid 0x20000000a createSession 30000
3/19/25, 12:30:39 PM UTC session 0x1000000b39e0002 cxid 0x2 zxid 0x20000000b create /demo,,[31,s{'world,'anyone}
],false,2
3/19/25, 12:30:57 PM UTC session 0x1000000b39e0002 cxid 0x4 zxid 0x20000000c create /demo/node1,,[31,s{'world,'anyone}
],false,1
3/19/25, 12:31:14 PM UTC session 0x1000000b39e0002 cxid 0x5 zxid 0x20000000d create /demo/node1/r-0000000000,,[31,s{'world,'anyone}
],false,1
3/19/25, 12:33:08 PM UTC session 0x1000000b39e0002 cxid 0x6 zxid 0x20000000e create /demo/node2,,[31,s{'world,'anyone}
],false,2
3/19/25, 12:33:24 PM UTC session 0x1000000b39e0002 cxid 0x7 zxid 0x20000000f delete '/demo/node2
3/19/25, 12:33:29 PM UTC session 0x1000000b39e0002 cxid 0x8 zxid 0x200000010 create /demo/node2,,[31,s{'world,'anyone}
],true,3
3/19/25, 12:33:48 PM UTC session 0x1000000b39e0002 cxid 0x9 zxid 0x200000011 error -108
3/19/25, 12:35:12 PM UTC session 0x1000000b39e0002 cxid 0xa zxid 0x200000012 create /demo/node3,,[31,s{'world,'anyone}
],false,4
3/19/25, 12:35:35 PM UTC session 0x1000000b39e0002 cxid 0xd zxid 0x200000013 error -101
3/19/25, 12:35:48 PM UTC session 0x1000000b39e0002 cxid 0xe zxid 0x200000014 create /demo/node3/r-0000000000,,[31,s{'world,'anyone}
],true,1
3/19/25, 1:19:47 PM UTC session 0x1000000b39e0002 cxid 0xf zxid 0x200000015 create /demo/node3/r-0000000001,,[31,s{'world,'anyone}
],true,2
3/19/25, 1:19:47 PM UTC session 0x1000000b39e0002 cxid 0x10 zxid 0x200000016 create /demo/node3/r-0000000002,,[31,s{'world,'anyone}
],true,3
3/19/25, 1:19:48 PM UTC session 0x1000000b39e0002 cxid 0x11 zxid 0x200000017 create /demo/node3/r-0000000003,,[31,s{'world,'anyone}
],true,4
3/19/25, 1:19:48 PM UTC session 0x1000000b39e0002 cxid 0x12 zxid 0x200000018 create /demo/node3/r-0000000004,,[31,s{'world,'anyone}
],true,5
3/19/25, 1:19:49 PM UTC session 0x1000000b39e0002 cxid 0x13 zxid 0x200000019 create /demo/node3/r-0000000005,,[31,s{'world,'anyone}
],true,6
3/19/25, 1:20:34 PM UTC session 0x1000000b39e0002 cxid 0x14 zxid 0x20000001a create /demo/node3/r-0000000006,,[31,s{'world,'anyone}
],false,7
3/19/25, 1:20:35 PM UTC session 0x1000000b39e0002 cxid 0x15 zxid 0x20000001b create /demo/node3/r-0000000007,,[31,s{'world,'anyone}
],false,8
3/19/25, 1:20:36 PM UTC session 0x1000000b39e0002 cxid 0x16 zxid 0x20000001c create /demo/node3/r-0000000008,,[31,s{'world,'anyone}
],false,9
3/19/25, 1:20:36 PM UTC session 0x1000000b39e0002 cxid 0x17 zxid 0x20000001d create /demo/node3/r-0000000009,,[31,s{'world,'anyone}
],false,10
3/19/25, 1:20:37 PM UTC session 0x1000000b39e0002 cxid 0x18 zxid 0x20000001e create /demo/node3/r-0000000010,,[31,s{'world,'anyone}
],false,11
3/19/25, 1:20:38 PM UTC session 0x1000000b39e0002 cxid 0x19 zxid 0x20000001f create /demo/node3/r-0000000011,,[31,s{'world,'anyone}
],false,12
3/19/25, 1:20:38 PM UTC session 0x1000000b39e0002 cxid 0x1a zxid 0x200000020 create /demo/node3/r-0000000012,,[31,s{'world,'anyone}
],false,13
3/19/25, 1:20:39 PM UTC session 0x1000000b39e0002 cxid 0x1b zxid 0x200000021 create /demo/node3/r-0000000013,,[31,s{'world,'anyone}
],false,14
3/19/25, 1:20:39 PM UTC session 0x1000000b39e0002 cxid 0x1c zxid 0x200000022 create /demo/node3/r-0000000014,,[31,s{'world,'anyone}
],false,15
EOF reached after 34 txns.
```

## 2.snapshot文件

```bash

java -cp "./zookeeper-3.9.3.jar:./*" org.apache.zookeeper.server.SnapshotFormatter /home/hello/docker/zookeeper/data1/data/version-2/snapshot.0

./zkSnapShotToolkit.sh /home/hello/docker/zookeeper/data1/data/version-2/snapshot.0
```

### 输出

```text

13:34:46.126 [main] INFO org.apache.zookeeper.server.persistence.SnapStream - zookeeper.snapshot.compression.method = CHECKED
13:34:46.143 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -
13:34:46.143 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -   ______                  _
13:34:46.143 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -  |___  /                 | |
13:34:46.143 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -     / /    ___     ___   | | __   ___    ___   _ __     ___   _ __
13:34:46.143 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -    / /    / _ \   / _ \  | |/ /  / _ \  / _ \ | '_ \   / _ \ | '__|
13:34:46.143 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -   / /__  | (_) | | (_) | |   <  |  __/ |  __/ | |_) | |  __/ | |
13:34:46.144 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -  /_____|  \___/   \___/  |_|\_\  \___|  \___| | .__/   \___| |_|
13:34:46.144 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -                                               | |
13:34:46.144 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -                                               |_|
13:34:46.144 [main] INFO org.apache.zookeeper.server.ZooKeeperServer -
13:34:46.150 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:zookeeper.version=3.9.3-c26634f34490bb0ea7a09cc51e05ede3b4e320ee, built on 2024-10-17 23:21 UTC
13:34:46.150 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:host.name=dev
13:34:46.150 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.version=17.0.14
13:34:46.150 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.vendor=Oracle Corporation
13:34:46.150 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.home=/home/hello/local/java/jdk-17.0.14
13:34:46.150 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.class.path=./zookeeper-3.9.3.jar:./jetty-util-9.4.56.v20240826.jar:./jetty-util-ajax-9.4.56.v20240826.jar:./zookeeper-jute-3.9.3.jar:./netty-codec-4.1.113.Final.jar:./metrics-core-4.1.12.1.jar:./netty-transport-classes-epoll-4.1.113.Final.jar:./netty-buffer-4.1.113.Final.jar:./netty-transport-native-epoll-4.1.113.Final-linux-x86_64.jar:./logback-core-1.2.13.jar:./audience-annotations-0.12.0.jar:./jetty-server-9.4.56.v20240826.jar:./netty-transport-native-unix-common-4.1.113.Final.jar:./netty-tcnative-boringssl-static-2.0.66.Final-windows-x86_64.jar:./jetty-servlet-9.4.56.v20240826.jar:./simpleclient_common-0.9.0.jar:./slf4j-api-1.7.30.jar:./netty-tcnative-classes-2.0.66.Final.jar:./jetty-io-9.4.56.v20240826.jar:./jetty-http-9.4.56.v20240826.jar:./jline-2.14.6.jar:./zookeeper-prometheus-metrics-3.9.3.jar:./javax.servlet-api-3.1.0.jar:./netty-tcnative-boringssl-static-2.0.66.Final-linux-aarch_64.jar:./netty-tcnative-boringssl-static-2.0.66.Final-linux-x86_64.jar:./zookeeper-3.9.3.jar:./simpleclient_servlet-0.9.0.jar:./jackson-core-2.15.2.jar:./jetty-security-9.4.56.v20240826.jar:./netty-tcnative-boringssl-static-2.0.66.Final-osx-aarch_64.jar:./logback-classic-1.2.13.jar:./snappy-java-1.1.10.5.jar:./commons-io-2.17.0.jar:./jackson-annotations-2.15.2.jar:./netty-tcnative-boringssl-static-2.0.66.Final.jar:./commons-cli-1.5.0.jar:./simpleclient_hotspot-0.9.0.jar:./simpleclient-0.9.0.jar:./netty-handler-4.1.113.Final.jar:./netty-transport-4.1.113.Final.jar:./netty-tcnative-boringssl-static-2.0.66.Final-osx-x86_64.jar:./netty-resolver-4.1.113.Final.jar:./jackson-databind-2.15.2.jar:./netty-common-4.1.113.Final.jar
13:34:46.150 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.library.path=/usr/java/packages/lib:/usr/lib64:/lib64:/lib:/usr/lib
13:34:46.150 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.io.tmpdir=/tmp
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:java.compiler=<NA>
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.name=Linux
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.arch=amd64
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.version=5.15.0-134-generic
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:user.name=hello
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:user.home=/home/hello
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:user.dir=/home/hello/local/zookeeper/apache-zookeeper-3.9.3-bin/lib
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.memory.free=501MB
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.memory.max=8016MB
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - Server environment:os.memory.total=512MB
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.enableEagerACLCheck = false
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.digest.enabled = true
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.closeSessionTxn.enabled = true
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.serializeLastProcessedZxid.enabled = true
13:34:46.151 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.flushDelay = 0 ms
13:34:46.152 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.maxWriteQueuePollTime = 0 ms
13:34:46.152 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.maxBatchSize=1000
13:34:46.152 [main] INFO org.apache.zookeeper.server.ZooKeeperServer - zookeeper.intBufferStartingSizeBytes = 1024
13:34:46.157 [main] INFO org.apache.zookeeper.server.watch.WatchManagerFactory - Using org.apache.zookeeper.server.watch.WatchManager as watch manager
13:34:46.158 [main] INFO org.apache.zookeeper.server.watch.WatchManagerFactory - Using org.apache.zookeeper.server.watch.WatchManager as watch manager
ZNode Details (count=5):
----
/
  cZxid = 0x00000000000000
  ctime = Thu Jan 01 00:00:00 UTC 1970
  mZxid = 0x00000000000000
  mtime = Thu Jan 01 00:00:00 UTC 1970
  pZxid = 0x00000000000000
  cversion = 0
  dataVersion = 0
  aclVersion = 0
  ephemeralOwner = 0x00000000000000
  dataLength = 0
----
/zookeeper
  cZxid = 0x00000000000000
  ctime = Thu Jan 01 00:00:00 UTC 1970
  mZxid = 0x00000000000000
  mtime = Thu Jan 01 00:00:00 UTC 1970
  pZxid = 0x00000000000000
  cversion = 0
  dataVersion = 0
  aclVersion = 0
  ephemeralOwner = 0x00000000000000
  dataLength = 0
----
/zookeeper/config
  cZxid = 0x00000000000000
  ctime = Thu Jan 01 00:00:00 UTC 1970
  mZxid = 0x00000000000000
  mtime = Thu Mar 06 02:17:06 UTC 2025
  pZxid = 0x00000000000000
  cversion = 0
  dataVersion = -1
  aclVersion = -1
  ephemeralOwner = 0x00000000000000
  dataLength = 156
----
/zookeeper/quota
  cZxid = 0x00000000000000
  ctime = Thu Jan 01 00:00:00 UTC 1970
  mZxid = 0x00000000000000
  mtime = Thu Jan 01 00:00:00 UTC 1970
  pZxid = 0x00000000000000
  cversion = 0
  dataVersion = 0
  aclVersion = 0
  ephemeralOwner = 0x00000000000000
  dataLength = 0
----
Session Details (sid, timeout, ephemeralCount):
----
Last zxid: 0x0
```

