# Redis基础使用

Redis 提供了丰富的数据结构，可以帮助开发者解决各种不同场景的问题。下面是 Redis 支持的常见数据结构及其应用：

---

# **1. 字符串（String）**
- **定义**：Redis 中最基本的数据类型，类似于普通的键值对。可以存储单一的字符串、整数或浮点数。
- **操作**：可以对字符串进行设置、获取、增减等操作。
  
  **常见操作**：
  ```bash
  SET key value        # 设置一个字符串
  GET key              # 获取字符串
  INCR key             # 增加值
  DECR key             # 减少值
  APPEND key value     # 字符串追加
  ```

---

# **2. 哈希（Hash）**
- **定义**：类似于 Java 中的 Map 或 Python 中的字典（dict），存储多个字段值（key-value）对。
- **应用**：适用于存储对象的字段，例如用户信息、配置项等。

  **常见操作**：
  ```bash
  HSET key field value    # 设置哈希字段的值
  HGET key field          # 获取哈希字段的值
  HGETALL key             # 获取哈希中的所有字段及其值
  HDEL key field          # 删除哈希字段
  HINCRBY key field num   # 对哈希字段的值进行自增
  ```

---

# **3. 列表（List）**
- **定义**：Redis 列表是一个简单的字符串列表，支持在列表的两端进行推送和弹出。
- **应用**：适用于消息队列、任务队列等场景。

  **常见操作**：
  ```bash
  LPUSH key value        # 向列表左侧推送一个元素
  RPUSH key value        # 向列表右侧推送一个元素
  LPOP key               # 从列表左侧弹出一个元素
  RPOP key               # 从列表右侧弹出一个元素
  LRANGE key start stop  # 获取列表的指定范围的元素
  ```

---

# **4. 集合（Set）**
- **定义**：Redis 集合是无序的字符串集合，不允许重复元素。
- **应用**：适用于去重、社交网络中“朋友关系”等场景。

  **常见操作**：
  ```bash
  SADD key member        # 向集合中添加元素
  SREM key member        # 从集合中移除元素
  SMEMBERS key           # 获取集合中的所有成员
  SISMEMBER key member   # 检查成员是否在集合中
  SUNION key1 key2       # 获取多个集合的并集
  ```

---

# **5. 有序集合（Sorted Set, ZSet）**
- **定义**：有序集合类似于集合，但每个成员都会关联一个**分数（score）**，集合中的元素会根据分数自动排序。
- **应用**：适用于排行榜、优先队列等场景。

  **常见操作**：
  ```bash
  ZADD key score member      # 向有序集合添加元素，带分数
  ZREM key member            # 从有序集合中移除元素
  ZRANGE key start stop      # 获取有序集合中指定范围的元素（按分数排序）
  ZSCORE key member         # 获取元素的分数
  ZRANK key member          # 获取元素的排名（从低到高）
  ```

---

# **6. 位图（Bitmap）**
- **定义**：位图是 Redis 对二进制数据的一种压缩存储方式，可以通过 bit 操作来处理大量的二进制数据。
- **应用**：适用于用户签到、活跃用户统计等场景。

  **常见操作**：
  ```bash
  SETBIT key offset value   # 设置指定偏移量的位值
  GETBIT key offset         # 获取指定偏移量的位值
  BITCOUNT key              # 统计位图中值为1的位数
  BITOP operation dest key1 [key2 ...]  # 对多个位图执行位操作
  ```

---

# **7. HyperLogLog**
- **定义**：HyperLogLog 是一种概率型数据结构，主要用于**基数统计**，能够高效地估算某个集合的唯一元素数量。
- **应用**：适用于流量分析、独立访客统计等场景。

  **常见操作**：
  ```bash
  PFADD key element        # 向 HyperLogLog 添加元素
  PFCOUNT key              # 获取 HyperLogLog 中元素的基数估算
  ```

---

# **8. 地理空间（Geo）**
- **定义**：Redis 提供了对地理位置数据的支持，可以存储地理坐标，并进行地理位置相关的计算。
- **应用**：适用于定位服务、附近用户查询等场景。

  **常见操作**：
  ```bash
  GEOADD key longitude latitude member  # 向地理空间添加成员
  GEODIST key member1 member2          # 获取两个成员之间的距离
  GEORADIUS key longitude latitude radius unit  # 获取指定范围内的成员
  ```

---

# **9. 流（Stream）**
- **定义**：Redis Stream 是一个基于消息队列的**日志结构**，它支持消息的存储、消费、追踪等功能。
- **应用**：适用于消息队列、事件追踪等场景。

  **常见操作**：
  ```bash
  XADD key * field1 value1 field2 value2  # 向流中添加消息
  XRANGE key start end                    # 获取指定范围的消息
  XREAD COUNT count STREAMS key last_id  # 读取流中的消息
  ```

---

# **10. 发布/订阅（Pub/Sub）**
- **定义**：Redis 提供了发布/订阅功能，允许客户端订阅频道并接收来自发布者的消息。
- **应用**：适用于聊天系统、事件通知等场景。

  **常见操作**：
  ```bash
  PUBLISH channel message   # 发布消息到频道
  SUBSCRIBE channel         # 订阅频道
  UNSUBSCRIBE channel       # 取消订阅频道
  ```

---

# **总结**
| 数据结构        | 描述                                                   | 典型应用                      |
|-----------------|--------------------------------------------------------|-------------------------------|
| **字符串**      | 存储单一字符串或数字，最基础的数据结构                    | 存储缓存数据、会话标识         |
| **哈希**        | 键值对集合，适合存储对象的多个属性字段                   | 用户信息存储、配置项存储       |
| **列表**        | 有序的字符串列表，支持两端插入和删除                    | 消息队列、任务队列             |
| **集合**        | 无序且不重复的字符串集合                                | 社交网络、去重、标签系统       |
| **有序集合**    | 有序的字符串集合，按分数排序                            | 排行榜、任务优先级             |
| **位图**        | 通过位操作存储大规模二进制数据                          | 用户签到、活跃用户统计         |
| **HyperLogLog** | 用于高效估算基数（独立元素数量）                        | 唯一用户统计、流量分析         |
| **地理空间**    | 存储地理坐标数据并进行空间计算                          | 定位服务、附近用户查询         |
| **流**          | 基于消息队列的日志结构，支持消息的存储、消费和追踪     | 消息队列、事件日志             |
| **发布/订阅**   | 支持发布和订阅模式，客户端可以实时接收消息              | 实时消息系统、通知系统         |
