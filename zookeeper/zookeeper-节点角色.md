# zokeeper-节点角色


## 1.节点状态

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
### 状态解释

| 状态（ServerState） | 说明 |
|--------------------|------|
| `LOOKING` | 正在进行 Leader 选举，还未确定谁是 Leader |
| `LEADING` | 自己是 Leader，开始处理写事务 |
| `FOLLOWING` | 自己是 Follower，接收 Leader 的 Proposal，参与事务确认 |
| `OBSERVING` | 自己是 Observer，只参与读请求和数据同步，不参与选举 |

## 2.节点角色

| 角色 | 说明 |
|------|------|
| **Leader** | 领导者，负责处理写请求、事务提议（Proposal）和数据同步 |
| **Follower** | 追随者，负责接收客户端读请求、参与投票、同步 Leader 的事务 |
| **Observer** | 观察者，和 Follower 类似，但不参与投票和写事务流程（适用于读多写少场景） |

### 1. **Leader**
- 选举胜出者。
- 接收客户端所有写请求。
- 发起 **事务 Proposal（提议）** 给其他节点（Follower/Observer）。
- 收集 Follower 的 ACK（过半即 commit）。
- 保证事务顺序一致性（Zxid 单调递增）。
- 负责数据同步给新加入的节点。

### 2. **Follower**
- 向 Leader 发送 ACK（事务确认）。
- 接收 Leader 的 Proposal 和 Commit 指令。
- 接收客户端读请求（如果开启本地读）。
- 参与 Leader 选举投票。
- 同步 Leader 状态，参与 quorum（多数派）机制。

### 3. **Observer**
- 接收客户端读请求（主要用于扩展读性能）。
- 同步 Leader 数据，但 **不参与投票**、不参与 Proposal ACK。
- 通常部署在“读多写少”的集群中，用于提升读扩展性、降低写压力。



