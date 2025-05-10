# MyBatis基础-resultMap

`<resultMap>` 是 MyBatis 中非常核心的标签之一，它定义了从数据库查询结果（ResultSet）映射到 Java 对象的详细规则，是 **高级结果映射** 的基础，解决字段名与属性名不一致、关联映射、嵌套对象等问题。

---

## 1. 基础结构

```xml
<resultMap id="userResultMap" type="com.example.User">
  <id property="id" column="user_id"/>
  <result property="username" column="user_name"/>
  <result property="email" column="email_address"/>
</resultMap>
```

* `id`: 主键字段，用 `<id>` 映射，MyBatis 会优先处理它。
* `result`: 普通字段映射。
* `type`: Java 对象的全限定类名。

---

## 2. 常用子标签详解

| 标签                | 用途描述                          |
| ----------------- | ----------------------------- |
| `<id>`            | 映射主键字段。MyBatis 会做缓存键生成优化。     |
| `<result>`        | 映射普通字段。字段名和属性名不一致时使用它。        |
| `<association>`   | 映射“has-a”关系的嵌套对象，适用于一对一关联。    |
| `<collection>`    | 映射“has-many”关系的嵌套集合，适用于一对多关联。 |
| `<constructor>`   | 用于将结果集映射到使用构造函数创建的对象。         |
| `<discriminator>` | 用于实现继承或条件分支映射（类似于 Java 中的多态）。 |

---

### 2.1 示例一：字段名和属性名不一致

```xml
<resultMap id="userMap" type="User">
  <id property="id" column="user_id"/>
  <result property="name" column="user_name"/>
  <result property="email" column="email_address"/>
</resultMap>
```

---

### 2.2 示例二：一对一关联（`<association>`）

```xml
<resultMap id="userMap" type="User">
  <id property="id" column="id"/>
  <result property="name" column="name"/>
  <association property="address" javaType="Address">
    <result property="city" column="city"/>
    <result property="street" column="street"/>
  </association>
</resultMap>
```

---

### 2.3 示例三：一对多关联（`<collection>`）

```xml
<resultMap id="departmentMap" type="Department">
  <id property="id" column="dept_id"/>
  <result property="name" column="dept_name"/>
  <collection property="employees" ofType="Employee">
    <id property="id" column="emp_id"/>
    <result property="name" column="emp_name"/>
  </collection>
</resultMap>
```

---

### 2.4 示例四：构造函数注入（`<constructor>`）

```xml
<resultMap id="userMap" type="User">
  <constructor>
    <idArg column="id" javaType="int"/>
    <arg column="name" javaType="String"/>
  </constructor>
</resultMap>
```

---

### 2.5 示例五：多态映射（`<discriminator>`）

```xml
<resultMap id="vehicleMap" type="Vehicle">
  <id property="id" column="id"/>
  <result property="type" column="type"/>
  <discriminator javaType="String" column="type">
    <case value="car" resultMap="carMap"/>
    <case value="bike" resultMap="bikeMap"/>
  </discriminator>
</resultMap>
```

---



