# MyBatis基础-动态SQL

MyBatis 动态 SQL 是其强大功能之一，允许在映射 XML 中使用类似编程语言的逻辑（如 `if`、`where`、`foreach` 等）来构建灵活 SQL 语句。它底层通过 Java 的 XML 语法和 OGNL 表达式来实现 SQL 的动态拼接。

---

## 1. 动态 SQL 标签

| 标签            | 作用                       |
| ------------- | ------------------------ |
| `<if>`        | 条件判断                     |
| `<choose>`    | 类似 Java 的 `switch-case`  |
| `<when>`      | `<choose>` 的子标签（case）    |
| `<otherwise>` | `<choose>` 的默认分支         |
| `<trim>`      | 自定义去除前后多余的拼接符            |
| `<where>`     | 自动添加 `WHERE` 并去除前缀 `AND` |
| `<set>`       | 用于动态 `UPDATE` 的 `SET` 子句 |
| `<foreach>`   | 遍历集合构建 `IN` 或批量插入语句      |
| `<bind>`      | 定义变量，支持 OGNL 表达式         |

---

## 2. 示例

### 2.1 `<if>` 条件判断

```xml
<select id="getUserList" resultType="User">
  SELECT * FROM user
  WHERE 1=1
  <if test="name != null">
    AND name = #{name}
  </if>
  <if test="status != null">
    AND status = #{status}
  </if>
</select>
```

---

### 2.2 `<choose>` 分支结构（类似 switch-case）

```xml
<select id="getUser" resultType="User">
  SELECT * FROM user
  <where>
    <choose>
      <when test="id != null">
        id = #{id}
      </when>
      <when test="email != null">
        email = #{email}
      </when>
      <otherwise>
        1 = 0
      </otherwise>
    </choose>
  </where>
</select>
```

---

### 2.3 `<trim>` 自定义去除多余拼接符

```xml
<update id="updateUser">
  UPDATE user
  <trim prefix="SET" suffixOverrides=",">
    <if test="name != null">name = #{name},</if>
    <if test="email != null">email = #{email},</if>
  </trim>
  WHERE id = #{id}
</update>
```

---

### 2.4 `<where>` 自动去除多余 AND/OR 并添加 WHERE

```xml
<select id="getUserList" resultType="User">
  SELECT * FROM user
  <where>
    <if test="name != null">AND name = #{name}</if>
    <if test="status != null">AND status = #{status}</if>
  </where>
</select>
```

---

### 2.5 `<set>` 自动去除最后一个逗号，用于 UPDATE

```xml
<update id="updateUser">
  UPDATE user
  <set>
    <if test="name != null">name = #{name},</if>
    <if test="email != null">email = #{email},</if>
  </set>
  WHERE id = #{id}
</update>
```

---

### 2.6 `<foreach>` 用于 IN 查询或批量插入

**IN 查询：**

```xml
<select id="getUsersByIds" resultType="User">
  SELECT * FROM user WHERE id IN
  <foreach collection="ids" item="id" open="(" separator="," close=")">
    #{id}
  </foreach>
</select>
```

**批量插入：**

```xml
<insert id="insertUsers">
  INSERT INTO user (name, email)
  VALUES
  <foreach collection="userList" item="u" separator=",">
    (#{u.name}, #{u.email})
  </foreach>
</insert>
```

---

### 2.7 `<bind>` 定义变量，结合 OGNL 表达式使用

```xml
<select id="searchUser" resultType="User">
  <bind name="pattern" value="'%' + keyword + '%'"/>
  SELECT * FROM user WHERE name LIKE #{pattern}
</select>
```

---


