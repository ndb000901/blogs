# zookeeper 配置项详解

ZooKeeper 的配置文件通常位于 `conf/zoo.cfg`，这是 ZooKeeper 的主要配置文件，其中包含了集群和节点的配置参数。以下是一些常见的 ZooKeeper 配置参数以及它们的说明：

### 基本配置项

1. **`tickTime`**  
   - **说明**：ZooKeeper 集群内部心跳的基本单位，单位是毫秒。`tickTime` 决定了 ZooKeeper 中心跳和超时的其他设置（例如 `initLimit` 和 `syncLimit`）。  
   - **默认值**：2000  
   - **示例**：
     ```properties
     tickTime=2000
     ```

2. **`dataDir`**  
   - **说明**：存储 ZooKeeper 数据（包括事务日志和快照）的目录。  
   - **默认值**：`/tmp/zookeeper`  
   - **示例**：
     ```properties
     dataDir=/var/lib/zookeeper
     ```

3. **`dataLogDir`**  
   - **说明**：存储 ZooKeeper 事务日志的目录。可以与 `dataDir` 分开设置来分担磁盘 IO。  
   - **默认值**：与 `dataDir` 相同  
   - **示例**：
     ```properties
     dataLogDir=/var/log/zookeeper
     ```

4. **`clientPort`**  
   - **说明**：客户端连接到 ZooKeeper 服务的端口，默认为 `2181`。  
   - **默认值**：2181  
   - **示例**：
     ```properties
     clientPort=2181
     ```

5. **`initLimit`**  
   - **说明**：Follower 节点与 Leader 节点建立连接的最大时间。单位是 `tickTime`。  
   - **默认值**：10  
   - **示例**：
     ```properties
     initLimit=10
     ```

6. **`syncLimit`**  
   - **说明**：Leader 节点和 Follower 节点之间同步的最大时间。单位是 `tickTime`。  
   - **默认值**：5  
   - **示例**：
     ```properties
     syncLimit=5
     ```

7. **`server.X`**  
   - **说明**：指定 ZooKeeper 集群中的节点，`X` 是节点编号，`server.X` 表示该节点的配置。包括节点的 IP 地址和端口信息。  
   - **示例**：
     ```properties
     server.1=192.168.1.1:2888:3888
     server.2=192.168.1.2:2888:3888
     server.3=192.168.1.3:2888:3888
     ```

8. **` quorumListenOnAllIPs`**  
   - **说明**：是否允许绑定所有网络接口。默认情况下，ZooKeeper 会绑定到所有的可用 IP 地址。  
   - **默认值**：`false`  
   - **示例**：
     ```properties
     quorumListenOnAllIPs=false
     ```

### 集群配置项

9. **`electionAlg`**  
   - **说明**：ZooKeeper 集群使用的选举算法。常见的值是 `0`（简单选举算法）和 `1`（高级选举算法）。  
   - **默认值**：0  
   - **示例**：
     ```properties
     electionAlg=0
     ```

10. **`leaderServes`**  
    - **说明**：是否允许 Leader 节点同时作为客户端请求的服务节点。如果为 `true`，Leader 会处理客户端请求，否则客户端请求将被转发到 Follower 节点。  
    - **默认值**：`true`  
    - **示例**：
      ```properties
      leaderServes=true
      ```

### 性能调优

11. **`maxClientCnxns`**  
    - **说明**：指定每个客户端可以建立的最大连接数。  
    - **默认值**：60  
    - **示例**：
      ```properties
      maxClientCnxns=100
      ```

12. **`autopurge.snapRetainCount`**  
    - **说明**：自动清除旧快照的数量。ZooKeeper 会定期删除不再需要的快照，以节省磁盘空间。  
    - **默认值**：3  
    - **示例**：
      ```properties
      autopurge.snapRetainCount=3
      ```

13. **`autopurge.purgeInterval`**  
    - **说明**：自动清除日志和快照的时间间隔，单位是小时。  
    - **默认值**：1  
    - **示例**：
      ```properties
      autopurge.purgeInterval=24
      ```

14. **`tickTime` 和 `syncLimit` 的关系**  
    - **说明**：`tickTime` 决定了 ZooKeeper 中心跳、超时以及其他许多配置的基础。通常，`syncLimit` 设置为 `tickTime` 的几倍，以确保 Leader 和 Follower 之间的同步是可接受的。

### 安全与认证

15. **`requireClientAuth`**  
    - **说明**：是否要求客户端进行身份认证才能连接到 ZooKeeper。  
    - **默认值**：`false`  
    - **示例**：
      ```properties
      requireClientAuthScheme=kerberos
      ```

16. **`kerberos` 配置**  
    - **说明**：如果启用了 Kerberos 认证，可以通过设置相关参数来启用与 Kerberos 的集成。  
    - **示例**：
      ```properties
      jaasLoginRenew=3600000
      ```

### 高可用性与故障转移

17. **`maxSessionTimeout`**  
    - **说明**：最大会话超时时间，超过该时间会话将被认为是失效的。  
    - **默认值**：60000（单位：毫秒）  
    - **示例**：
      ```properties
      maxSessionTimeout=60000
      ```

18. **`minSessionTimeout`**  
    - **说明**：最小会话超时时间，低于该时间会话将会被关闭。  
    - **默认值**：4000（单位：毫秒）  
    - **示例**：
      ```properties
      minSessionTimeout=4000
      ```

### 其他常用配置

19. **`snapshotBytes`**  
    - **说明**：ZooKeeper 使用的每个快照文件的最大大小。  
    - **默认值**：0（表示没有限制）  
    - **示例**：
      ```properties
      snapshotBytes=104857600
      ```

20. **`skipACL`**  
    - **说明**：是否跳过访问控制列表（ACL）检查。  
    - **默认值**：`false`  
    - **示例**：
      ```properties
      skipACL=true
      ```
