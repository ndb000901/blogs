# zookeeper-选举数据

## 1.选举数据

`ServerState.java`

[源码地址](./https://github.com/apache/zookeeper/blob/release-3.9.3/zookeeper-server/src/main/java/org/apache/zookeeper/server/quorum/QuorumPeer.java)

```java
public enum ServerState {
    LOOKING,
    FOLLOWING,
    LEADING,
    OBSERVING
}
```

`Vote.java`

[源码地址](https://github.com/apache/zookeeper/blob/release-3.9.3/zookeeper-server/src/main/java/org/apache/zookeeper/server/quorum/Vote.java)

```java

public class Vote {
    
    private final int version;

    private final long id;

    private final long zxid;

    private final long electionEpoch;

    private final long peerEpoch;
    
    private final ServerState state;
    
}
```
### 字段说明

| 字段名             | 含义                                                                                   |
|-----------------|--------------------------------------------------------------------------------------|
| `version`       | **投票版本号**，用于兼容不同的投票结构版本（ZooKeeper 在演进过程中可能升级 Vote 的格式）。                              |
| `id`            | **投票给哪个服务器的服务器ID（即候选Leader的ID）**，比如这个投票是“我认为 server.X 应该是 Leader”。                   |
| `zxid`          | **事务ID（ZooKeeper Transaction ID）**，表示这个候选人处理过的最新事务，代表“数据新旧程度”。选举时，优先选 **zxid 大的节点**。 |
| `electionEpoch` | **选举轮次/投票轮次**，每发起一次新的 Leader 选举就会递增。防止旧选举影响新选举。                                      |
| `peerEpoch`     | **Epoch纪元值（用于集群数据一致性判断）**，它代表该节点“最新的 epoch”，用于 Leader 确认时判断“数据同步的主版本号”。              |
| `state`         | **投票节点状态**                                                                           |
---

### 每个字段举例说明：

#### `id`：
- 假设我（server.2）发出一票：我认为 server.3 是 leader，我就会投 `id=3`

#### `zxid`：
- 这是 ZooKeeper 中用来标识事务先后的 ID，一般格式是 `zxid = (epoch << 32) + counter`
  - 前32位是 epoch（领导周期）
  - 后32位是事务计数器

> 选举时，优先比较 zxid，谁的 zxid 大，说明它数据更新更多，更“可靠”，优先当 Leader。

#### `electionEpoch`：
- 本轮选举的轮次编号，比如现在是第5轮选举了，就是 `electionEpoch=5`，防止旧轮投票干扰。

#### `peerEpoch`：
- 代表当前节点最近“集群任期版本号”，类似于 Paxos 或 Raft 中的 term。它表示“集群的版本进度”，也会影响 Leader 确认。

---

### 选举流程

```
每个Server发起投票(Vote)：
 -> 投出：Vote(id=X, zxid=A, electionEpoch=E, peerEpoch=P)

各Server互相对比Vote：
 -> 谁的 zxid 更大 => 赢
 -> zxid 相同 => 比 id（server id 更大者胜）
 -> electionEpoch 确保是同一轮
 -> peerEpoch 用于最终 Leader 确认阶段
```

