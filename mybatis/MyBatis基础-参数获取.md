# Mybatis基础-参数获取

---

## 1. 参数传递方式

### 1.1 **单个参数**
当 Mapper 接口方法只传入一个参数时（非集合、非数组）：
- 可以在 `mapper.xml` 中**直接使用**该参数名（任意名称）。
- 或使用 `#{param1}` 也是可以的。

**示例：**

```java
User selectById(Integer id);
```

对应 `UserMapper.xml`:

```xml
<select id="selectById" resultType="User">
  SELECT * FROM user WHERE id = #{id}
</select>
```

---

### 1.2 **多个参数（基本类型）**
MyBatis 会将多个参数封装为一个 Map，key 分别为：
- `param1`, `param2`, ...
- `arg0`, `arg1`, ...
- 如果使用 `@Param("自定义名称")`，则使用自定义名称。

**示例：**

```java
User selectByNameAndAge(@Param("name") String name, @Param("age") int age);
```

对应 XML：

```xml
<select id="selectByNameAndAge" resultType="User">
  SELECT * FROM user WHERE name = #{name} AND age = #{age}
</select>
```

---

### 1.3 **实体类参数**
传入一个 JavaBean/POJO，MyBatis 会按属性名取值。

**示例：**

```java
int insertUser(User user);
```

XML：

```xml
<insert id="insertUser">
  INSERT INTO user (name, age) VALUES (#{name}, #{age})
</insert>
```

---

### 1.4 **Map 参数**
传入 `Map<String, Object>`，MyBatis 会按键取值。

**示例：**

```java
User selectByMap(Map<String, Object> map);
```

XML：

```xml
<select id="selectByMap" resultType="User">
  SELECT * FROM user WHERE name = #{name} AND age = #{age}
</select>
```

---

### 1.5 **List、数组参数**
用于 `IN` 查询等：

#### List：

```java
List<User> selectByIds(@Param("ids") List<Integer> ids);
```

```xml
<select id="selectByIds" resultType="User">
  SELECT * FROM user WHERE id IN
  <foreach collection="ids" item="id" open="(" close=")" separator=",">
    #{id}
  </foreach>
</select>
```

#### 数组：

```java
List<User> selectByArray(@Param("ids") Integer[] ids);
```

---

## 2. `@Param` 注解
- 告诉 MyBatis 参数名称，用于 XML 中引用。
- 多个参数时**强烈推荐使用**，否则只能使用 `param1`, `param2`。

---

