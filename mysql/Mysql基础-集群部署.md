# Mysql基础-集群部署


## 1. MySQL 集群方案分类

| 类型 | 架构 | 适用场景 | 优点 | 缺点 |
|------|------|----------|------|------|
| **MySQL Group Replication** | Paxos-based 多主集群 | 高可用、强一致性 | 自动选主、故障切换快 | 写扩展差 |
| **MySQL InnoDB Cluster** | Group Replication + 管理套件 | 生产级原生 HA | 自动管理、强一致 | 运维成本 |
| **MySQL NDB Cluster** | 分布式共享无结构（NDB引擎） | 电信级高可用 | 自动分片、秒级故障恢复 | 不支持InnoDB，应用改造大 |
| **Galera Cluster (Percona/XtraDB)** | 同步复制 + 多主 | 高一致性要求 | 所有节点读写、强同步 | 网络要求高 |
| **MGR + Sharding** | 自研分库分表 + MGR | 大数据量分布式 | 自定义灵活 | 需开发数据层 |
| **中间件方案（如 ShardingSphere、Vitess、MyCat）** | 应用层代理分库分表 | OLTP/互联网业务 | 透明扩展、SQL兼容好 | 运维复杂、性能依赖中间件 |

---

## 2. 主流方案原理详解

### 2.1 Group Replication（MGR）

- **MySQL 5.7+ 引入的强一致性原生集群方案**
- 基于 **Paxos协议** 实现 **自动主从管理、选主、写冲突检测**

**核心特性**：
- 自动成员发现和重连
- 写冲突自动回滚
- 多主 or 单主模式
- 强一致性（同步或半同步）

```text
节点1 <==> 节点2 <==> 节点3
|-------- Paxos 协议 --------|
```

**部署建议**：
- 奇数节点（3个或5个）
- 配合 InnoDB Cluster 实现高自动化

---

### 2.2 InnoDB Cluster

**是在 MGR 基础上集成的官方高可用解决方案**：

组成：
- MySQL Server（启用 Group Replication）
- MySQL Shell（自动化运维）
- MySQL Router（透明读写路由）

优势：
- 全官方生态
- 自动主备切换、健康检查
- 搭配 Router 透明读写路由，适合中小型集群

---

### 2.3 Galera Cluster（Percona / MariaDB）

**三节点强同步集群（Active-Active）**

- 所有节点都能读写
- 写入事务必须获得多数节点 ACK，才能提交

优点：
- 写一致性高、部署简单

缺点：
- 网络抖动可能导致阻塞
- 不适合高并发大写量（写扩展差）

---

### 2.4 NDB Cluster（MySQL Cluster）

- 使用 **NDB 存储引擎** 的分布式 MySQL 集群
- 数据和计算分离，**高并发、实时性强**

结构：
- SQL Node（前端MySQL服务）
- Data Node（NDB数据存储）
- Management Node（管理进程）

优点：
- 自动数据分片与冗余
- 高可用、横向扩展强

缺点：
- 不支持 InnoDB，需要改写应用
- 学习和维护成本较高

---

## 3. 与主从复制对比

| 特性         | 主从复制   | MGR / Galera / NDB |
|--------------|-----------|-----------------|
| 高可用       | 手动切换   | 自动切换        |
| 强一致性     | 异步 / 半同步 | 支持强同步       |
| 写扩展       |   不支持        |（大多不支持）      |
| 容错能力     | 中         | 高              |
| 运维复杂度   | 低         | 中~高           |

---

## 4. 数据分片型集群方案（海量数据）

如果你业务有 **千万级~亿级表数据**，通常结合 **中间件分片+MGR 高可用**：

### 可选方案：
| 名称 | 特点 |
|------|------|
| **ShardingSphere** | Java生态好、兼容SQL |
| **Vitess（YouTube开源）** | 支持 Kubernetes + SQL 路由 |
| **MyCat** | 经典方案 |
| **TiDB** | 分布式 HTAP 引擎（不兼容InnoDB） |

架构：

```text
App → ShardingProxy → 多分库（每个库内部是主从或MGR）
```

---

## 5. 架构建议（按业务场景）

### 场景一：中小型系统、高可用要求
- InnoDB Cluster + MySQL Router

### 场景二：中型电商系统，读多写少
- 主从复制 + ProxySQL 或 MyCat 分库

### 场景三：强一致金融系统
- Galera Cluster + 3 节点 + 读写分离

### 场景四：海量写入（如推送、日志、IoT）
- ShardingSphere + 分库分表 + 单库 MGR

### 场景五：实时高并发分析
- 使用 **TiDB** 或引入 ClickHouse、Elasticsearch 等异构系统

---

## 6. MySQL 集群常用工具

| 工具 | 功能 |
|------|------|
| MySQL Shell | InnoDB Cluster 部署 |
| MySQL Router | 自动路由连接读写实例 |
| Orchestrator | 自动主从切换，图形化 |
| ProxySQL | 高性能数据库中间件 |
| ShardingSphere | 分库分表平台 |
| MHA | 主库故障自动转移（传统方案） |

---

## 7. 补充内容

### 主从延迟检测
```sql
SHOW SLAVE STATUS\G
-- Seconds_Behind_Master 监控延迟
```

### 自动故障切换示意
```text
Master 宕机
↓
从库自动接管（Orchestrator / MGR）
↓
MySQL Router 自动路由更新
↓
业务无感知
```

---

