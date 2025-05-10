# MyBatis基础 缓存

在 MyBatis 中，缓存（Cache）是提升查询性能的重要机制，主要分为两级：

---

## 1. 一级缓存（本地缓存 Local Cache）

### 特点

* 默认开启
* 作用范围：**SqlSession** 级别
* 缓存内容：同一个 `SqlSession` 内部执行相同的查询语句，第二次直接从缓存中取，避免数据库访问

### 生命周期

* 同一个 `SqlSession` 中缓存有效
* `SqlSession.close()` 后，缓存清除
* `SqlSession.commit()` 或 `SqlSession.rollback()` 之后也会清空缓存

### 示例行为

```java
User u1 = session.selectOne("getUser", 1);
User u2 = session.selectOne("getUser", 1); // 不会发起 SQL，走缓存
```

---

## 2. 二级缓存（全局缓存）

### 特点

* **作用范围：Mapper namespace 级别**
* 默认关闭，需要配置启用
* 跨 `SqlSession` 共享
* 基于内存或第三方缓存（如 EhCache、Redis 等）

---

## 3. 启用二级缓存

### 3.1 配置 `<settings>` 开启总开关

```xml
<setting name="cacheEnabled" value="true"/>
```

### 3.2 在 Mapper XML 中添加 `<cache/>` 标签

```xml
<mapper namespace="com.example.UserMapper">
  <cache
    eviction="LRU"
    flushInterval="60000"
    size="512"
    readOnly="false"/>
</mapper>
```

#### 常用属性说明：

| 属性              | 描述                                       |
| --------------- | ---------------------------------------- |
| `eviction`      | 缓存回收策略，可选：`LRU`（默认）、`FIFO`、`SOFT`、`WEAK` |
| `flushInterval` | 刷新间隔（毫秒），默认不自动刷新                         |
| `size`          | 缓存上限对象数                                  |
| `readOnly`      | 只读缓存，若为 `true`，返回的是缓存对象的引用（高性能，低安全）      |

---

## 4. 自定义二级缓存实现

### 4.1 实现接口：`org.apache.ibatis.cache.Cache`

```java
public class MyCustomCache implements Cache {
  // 实现 putObject、getObject、removeObject 等方法
}
```

### 4.2 在 mapper.xml 中配置：

```xml
<cache type="com.example.MyCustomCache"/>
```

---

## 5. 缓存失效情况

| 情况                        | 会清空缓存？               |
| ------------------------- | -------------------- |
| `insert/update/delete` 操作 | 是，影响对应 namespace 的缓存 |
| `commit` / `rollback`     | 是                    |
| 不同 `SqlSession` 中查询       | 一级缓存无效，需使用二级缓存       |
| 手动清除                      | 调用 `clearCache()` 方法 |

---

## 6. cache-ref 标签：多 Mapper 共享缓存

```xml
<mapper namespace="com.example.OrderMapper">
  <cache-ref namespace="com.example.UserMapper"/>
</mapper>
```

---


