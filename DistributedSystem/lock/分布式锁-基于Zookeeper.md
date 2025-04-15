# 分布式锁-基于Zookeeper


## 1. **Zookeeper 分布式锁原理**

Zookeeper 提供的 **节点类型** 和 **节点操作**，使得我们能够轻松实现分布式锁。最常见的分布式锁实现方式基于以下两个核心概念：

1. **临时节点（Ephemeral Node）**：临时节点在客户端与 Zookeeper 会话断开时自动删除。基于这一特性，锁可以在客户端崩溃时自动释放，无需额外的锁释放操作。

2. **顺序节点（Sequential Node）**：顺序节点的名字是带有自增序号的，可以确保节点的创建顺序。通过比较当前节点与其他节点的序号，Zookeeper 能够确保只有序号最小的节点能够获取到锁。

## 2. **实现分布式锁的步骤**

基于 Zookeeper 实现分布式锁的基本步骤如下：

1. **创建锁节点**：
   在 Zookeeper 上创建一个临时顺序节点，用于标识一个客户端请求锁的过程。

2. **判断是否为最小节点**：
   客户端创建完节点后，会列出当前锁目录下所有节点，比较当前节点是否是最小的顺序节点。如果是最小节点，则表示客户端可以获得锁。

3. **等待前一个节点释放锁**：
   如果当前节点不是最小节点，则客户端需要监听排在自己前面的那个节点，等待它释放锁。一旦前一个节点被删除，当前节点会重新检查是否是最小节点。

4. **释放锁**：
   当客户端完成任务后，删除自己在 Zookeeper 中创建的锁节点，释放锁。此时，排在它后面的客户端可以继续尝试获取锁。

## 3. **Zookeeper 分布式锁实现流程**

1. **客户端请求获取锁**：
   客户端请求锁时，在指定的锁路径下创建一个临时顺序节点。

   ```bash
   /locks/lock-000000001
   ```

2. **列出锁路径下的所有节点**：
   客户端获取到锁后，会列出锁路径 `/locks/` 下的所有节点，并按照节点的顺序进行排序。

   ```bash
   /locks/lock-000000001
   /locks/lock-000000002
   /locks/lock-000000003
   ```

3. **判断是否为最小节点**：
   如果当前节点是最小的（即 `lock-000000001`），则客户端获得锁；如果不是，则它需要监听排在自己前面的节点。

4. **等待前一个节点删除**：
   客户端会在 Zookeeper 上监听排在自己前面的节点（如 `lock-000000002`），等待它被删除。如果该节点被删除，客户端重新判断自己是否成为最小节点，如果是，则获取锁。

5. **释放锁**：
   客户端执行任务完毕后，会删除自己在 Zookeeper 上的临时节点，释放锁。

## 4. **Zookeeper 实现分布式锁的核心代码**

```java
import org.apache.zookeeper.*;

import java.io.IOException;
import java.util.List;
import java.util.Collections;

public class ZookeeperDistributedLock {
    private static final String ZK_SERVER = "localhost:2181";  // Zookeeper 地址
    private static final String LOCK_PATH = "/locks";          // 锁的根路径
    private static final String LOCK_PREFIX = "lock-";         // 锁节点的前缀
    private static ZooKeeper zkClient;
    
    public static void main(String[] args) throws Exception {
        // 连接 Zookeeper
        zkClient = new ZooKeeper(ZK_SERVER, 3000, new Watcher() {
            public void process(WatchedEvent event) {
                // 监听事件的回调方法
            }
        });

        String lockNode = acquireLock();
        System.out.println("获得锁: " + lockNode);

        // 执行任务
        performTask();

        releaseLock(lockNode);
        System.out.println("释放锁: " + lockNode);
    }

    // 获取锁
    public static String acquireLock() throws KeeperException, InterruptedException {
        String lockNode = zkClient.create(LOCK_PATH + "/" + LOCK_PREFIX,
                new byte[0], ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
        System.out.println("创建锁节点: " + lockNode);

        // 获取所有锁节点
        List<String> children = zkClient.getChildren(LOCK_PATH, false);
        Collections.sort(children);

        // 检查当前节点是否是最小的顺序节点
        if (lockNode.equals(LOCK_PATH + "/" + children.get(0))) {
            return lockNode; // 如果是最小的节点，获取到锁
        }

        // 监听排在自己前面的节点
        int index = children.indexOf(lockNode.substring(LOCK_PATH.length() + 1));
        String prevNode = LOCK_PATH + "/" + children.get(index - 1);

        // 监听前一个节点
        zkClient.exists(prevNode, new Watcher() {
            public void process(WatchedEvent event) {
                try {
                    acquireLock();  // 重新尝试获取锁
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });

        return null; // 如果没有获取到锁，则返回 null
    }

    // 执行任务
    public static void performTask() {
        // 模拟任务执行
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    // 释放锁
    public static void releaseLock(String lockNode) throws KeeperException, InterruptedException {
        zkClient.delete(lockNode, -1); // 删除锁节点
    }
}
```

## 5. **Zookeeper 分布式锁的优势**

1. **高可靠性**：
   Zookeeper 是一个分布式协调系统，具有高可用性和强一致性。它可以保证锁操作的原子性和一致性。

2. **自动释放锁**：
   通过临时节点特性，在客户端与 Zookeeper 的连接断开时，锁会自动释放，避免死锁问题。

3. **可扩展性**：
   Zookeeper 的分布式架构使得其在集群中具有很好的扩展性，能够支持大量的并发请求。

4. **顺序性保证**：
   顺序节点可以确保客户端按顺序获取锁，避免了无序争抢锁的情况，保证了锁的公平性。

## 6. **Zookeeper 分布式锁的缺点**

1. **性能瓶颈**：
   Zookeeper 是一个强一致性的系统，在高并发情况下，频繁的网络通信和节点操作可能导致性能瓶颈。

2. **单点故障**：
   尽管 Zookeeper 支持高可用架构，但 Zookeeper 集群仍然存在单点故障的风险，特别是在没有合理配置集群时。

3. **复杂性**：
   使用 Zookeeper 作为分布式锁的实现，可能需要更多的配置和调试，尤其是在跨多个数据中心时。

