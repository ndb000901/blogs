MyBatis基础-增删改查


## 1. select

在 MyBatis 中，`<select>` 是最常用的 SQL 映射语句标签之一，它用于执行数据库的查询操作。相比直接写 SQL，MyBatis 提供了灵活的 XML 映射和注解方式，可以动态生成 SQL，自动映射结果，支持缓存等功能。

---

### 1.1 基本语法

XML 配置方式：

```xml
<select id="getUserById" parameterType="long" resultType="com.example.User">
    SELECT * FROM user WHERE id = #{id}
</select>
```

---

### 1.2 属性详解

| 属性              | 说明                                             |
| --------------- | ---------------------------------------------- |
| `id`            | 映射语句的唯一标识（对应接口的方法名）                            |
| `parameterType` | 输入参数类型（Java类型，全限定名或别名）                         |
| `resultType`    | 返回结果的类型（常用 JavaBean 类、Map、基本类型等）               |
| `resultMap`     | 使用 `<resultMap>` 映射复杂结构结果时使用（优先于 `resultType`） |
| `flushCache`    | 是否刷新缓存（默认 `select=false`，即不刷新）                 |
| `useCache`      | 是否启用二级缓存（默认 `true`，前提是 mapper 开启了缓存）           |
| `timeout`       | 超时时间（单位：秒）                                     |
| `fetchSize`     | 建议驱动每次获取的数据条数，调优使用                             |

---

### 1.3 占位符：`#{} vs ${}`

* `#{}`：**预编译**参数，安全（防止 SQL 注入），适合传入值。
* `${}`：**字符串拼接**，不安全，适合传入列名或表名。

示例：

```xml
<!-- 安全传参 -->
SELECT * FROM user WHERE id = #{id}

<!-- 动态表名 -->
SELECT * FROM ${tableName} WHERE id = #{id}
```

---

### 1.4 返回结果类型

* 单个对象：返回单个 JavaBean
* 集合类型：返回 List<T>
* Map 类型：可以使用 `@MapKey` 注解将结果按某列转为 Map
* 自定义结构：使用 `<resultMap>` 支持嵌套映射、联合查询

---

### 1.5 动态 SQL 支持

可结合 `<if>`、`<choose>`、`<trim>`、`<where>` 等标签动态构建 SQL：

```xml
<select id="findUsers" resultType="User">
  SELECT * FROM user
  <where>
    <if test="name != null">AND name = #{name}</if>
    <if test="email != null">AND email = #{email}</if>
  </where>
</select>
```

---

### 1.6 多参数传入方式

* 使用 JavaBean 作为参数对象
* 使用 `@Param` 注解绑定多个参数名

```java
@Select("SELECT * FROM user WHERE name = #{name} AND age = #{age}")
User findByNameAndAge(@Param("name") String name, @Param("age") int age);
```

---

### 1.7 注解方式

```java
@Select("SELECT * FROM user WHERE id = #{id}")
User getUserById(Long id);
```

---

### 1.8 二级缓存控制（`<cache>`）

在 Mapper XML 中配置：

```xml
<cache eviction="LRU" flushInterval="60000" size="512" readOnly="false"/>
```

然后在 `<select>` 中使用 `useCache="true"`：

```xml
<select id="getUserById" resultType="User" useCache="true">
  SELECT * FROM user WHERE id = #{id}
</select>
```

---

### 1.9 示例：带分页和条件查询

```xml
<select id="findUsers" resultType="User" parameterType="map">
  SELECT * FROM user
  <where>
    <if test="status != null">AND status = #{status}</if>
    <if test="name != null">AND name LIKE CONCAT('%', #{name}, '%')</if>
  </where>
  ORDER BY create_time DESC
  LIMIT #{offset}, #{limit}
</select>
```

---


## 2. insert

在 MyBatis 中，`<insert>` 是用于执行新增数据操作的核心 SQL 标签。它不仅支持基础的 SQL 插入语句，还可以结合动态 SQL、自动生成主键、事务控制、批量插入等功能，十分灵活。

---

### 2.1 基本语法

```xml
<insert id="insertUser" parameterType="com.example.User">
  INSERT INTO user(name, email, status)
  VALUES(#{name}, #{email}, #{status})
</insert>
```

---

### 2.2 常用属性说明

| 属性                 | 说明                                        |
| ------------------ | ----------------------------------------- |
| `id`               | 唯一标识，对应 Mapper 接口的方法名                     |
| `parameterType`    | 入参类型，JavaBean、Map 或基本类型                   |
| `useGeneratedKeys` | 是否使用数据库自增主键（MySQL 常用）                     |
| `keyProperty`      | 将数据库生成的主键赋值给哪个 Java 属性                    |
| `keyColumn`        | 数据库中对应的主键列名（可选）                           |
| `flushCache`       | 是否刷新缓存（默认 `true`）                         |
| `statementType`    | STATEMENT/PREPARED/CALLABLE（默认为 PREPARED） |

---

### 2.3 主键返回（常用于 MySQL 的自增主键）

```xml
<insert id="insertUser" parameterType="User" useGeneratedKeys="true" keyProperty="id">
  INSERT INTO user(name, email) VALUES(#{name}, #{email})
</insert>
```

等价于 JDBC 的 `Statement.RETURN_GENERATED_KEYS`，可以将生成的 `id` 设置回传给 `user.setId(...)`。

---


### 2.4 批量插入

手动构造批量插入（适用于小批量场景）：

```xml
<insert id="batchInsertUser" parameterType="list">
  INSERT INTO user(name, email) VALUES
  <foreach collection="list" item="user" separator=",">
    (#{user.name}, #{user.email})
  </foreach>
</insert>
```

结合 Spring Boot 时，建议用 MyBatis Plus、或者 MyBatis 批处理模式。

---

### 2.5 动态插入（插入非空字段）

```xml
<insert id="insertSelective" parameterType="User">
  INSERT INTO user
  <trim prefix="(" suffix=")" suffixOverrides=",">
    <if test="name != null">name,</if>
    <if test="email != null">email,</if>
    <if test="status != null">status,</if>
  </trim>
  <trim prefix="VALUES(" suffix=")" suffixOverrides=",">
    <if test="name != null">#{name},</if>
    <if test="email != null">#{email},</if>
    <if test="status != null">#{status},</if>
  </trim>
</insert>
```

---

## 3. update

在 MyBatis 中，`<update>` 标签用于执行更新（UPDATE）语句，功能强大、灵活，支持动态 SQL、条件更新、自动刷新缓存、事务控制等。

---

### 3.1 基本语法

```xml
<update id="updateUser" parameterType="com.example.User">
  UPDATE user SET name = #{name}, email = #{email} WHERE id = #{id}
</update>
```

---

### 3.2 常用属性

| 属性              | 说明                                             |
| --------------- | ---------------------------------------------- |
| `id`            | SQL 映射的唯一标识，通常对应 Mapper 接口中的方法名                |
| `parameterType` | 入参类型，支持 JavaBean、Map、基本类型等                     |
| `flushCache`    | 是否刷新一级/二级缓存（默认为 `true`，建议保留）                   |
| `statementType` | 默认 `PREPARED`，可选值有 STATEMENT、PREPARED、CALLABLE |
| `timeout`       | 超时时间（单位：秒）                                     |

---

### 3.3 返回值类型

通常返回 `int`，表示受影响的行数：

```java
int updateUser(User user);
```

---

### 3.4 动态更新（只更新非空字段）

```xml
<update id="updateSelective" parameterType="User">
  UPDATE user
  <set>
    <if test="name != null">name = #{name},</if>
    <if test="email != null">email = #{email},</if>
    <if test="status != null">status = #{status},</if>
  </set>
  WHERE id = #{id}
</update>
```

* `<set>` 标签会自动处理结尾的多余逗号。
* `<if>` 是动态 SQL 的经典用法。

---


### 3.5 结合 `@Param` 使用（多个参数）

```java
int updateStatus(@Param("id") Long id, @Param("status") Integer status);
```

```xml
<update id="updateStatus" parameterType="map">
  UPDATE user SET status = #{status} WHERE id = #{id}
</update>
```

---


### 3.6 批量更新（简单案例）

```xml
<update id="batchUpdateStatus" parameterType="list">
  <foreach collection="list" item="user" separator=";">
    UPDATE user SET status = #{user.status} WHERE id = #{user.id}
  </foreach>
</update>
```

> 注意：MySQL 中多个 `UPDATE` 语句需用 `;` 分隔；性能不如单条大 SQL 更新。



## 4. delete


在 MyBatis 中，`<delete>` 标签用于执行 **删除（DELETE）语句**，其使用方式类似于 `<select>`、`<insert>`、`<update>`，常用于删除单条或多条记录，支持动态 SQL 和缓存控制等功能。

---

### 4.1 基本语法

```xml
<delete id="deleteUserById" parameterType="long">
  DELETE FROM user WHERE id = #{id}
</delete>
```

---

### 4.2 属性详解

| 属性名             | 描述                                                          |
| --------------- | ----------------------------------------------------------- |
| `id`            | 映射到 Mapper 接口的方法名                                           |
| `parameterType` | 传入参数类型，可以是基本类型、Map、POJO 等                                   |
| `flushCache`    | 是否刷新缓存，默认 `true`                                            |
| `timeout`       | SQL 超时时间（秒）                                                 |
| `statementType` | 执行方式，默认 `PREPARED`，可选值有：`STATEMENT`, `PREPARED`, `CALLABLE` |

---

### 4.3 返回值类型

MyBatis 的 `<delete>` 默认返回 `int` 类型，表示删除影响的记录条数。

```java
int deleteUserById(Long id);
```

---

### 4.4 批量删除

### 方法一：使用 `foreach`

```xml
<delete id="deleteByIds" parameterType="list">
  DELETE FROM user WHERE id IN
  <foreach collection="list" item="id" open="(" separator="," close=")">
    #{id}
  </foreach>
</delete>
```

调用：

```java
List<Long> ids = Arrays.asList(1L, 2L, 3L);
mapper.deleteByIds(ids);
```

---

### 4.5 动态删除（危险！小心全表删）

```xml
<delete id="deleteByCondition" parameterType="User">
  DELETE FROM user
  <where>
    <if test="status != null">AND status = #{status}</if>
    <if test="name != null">AND name = #{name}</if>
  </where>
</delete>
```

* `<where>` 标签自动去掉第一个 `AND`
* **注意：如果所有条件为 null，会变成 `DELETE FROM user`，全表删除！应加兜底逻辑**

---



