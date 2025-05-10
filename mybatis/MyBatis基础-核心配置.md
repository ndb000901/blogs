# MyBatis基础-核心配置

## 1. 核心配置文件

`mybatis-config.xml`

```xml

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!-- 1. 属性配置 -->
    <properties resource="db.properties" />

    <!-- 2. 设置项 -->
    <settings>
        <setting name="cacheEnabled" value="true"/>
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!-- 更多设置项见后文说明 -->
    </settings>

    <!-- 3. 类型别名 -->
    <typeAliases>
        <package name="com.example.model"/>
    </typeAliases>

    <!-- 4. 类型处理器 -->
    <typeHandlers>
        <package name="com.example.typehandler"/>
    </typeHandlers>

    <!-- 5. 插件 -->
    <plugins>
        <plugin interceptor="com.example.MyInterceptor"/>
    </plugins>

    <!-- 6. 环境配置（多数据源支持） -->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${db.driver}" />
                <property name="url" value="${db.url}" />
                <property name="username" value="${db.username}" />
                <property name="password" value="${db.password}" />
            </dataSource>
        </environment>
    </environments>

    <!-- 7. 映射器 -->
    <mappers>
        <package name="com.example.mapper"/>
    </mappers>

</configuration>

```

## 2. properties(属性)

`<properties>` 是 MyBatis 配置中最常见的模块之一，主要用于**定义或引入外部属性**，以便在配置文件中通过 `${key}` 形式引用变量（如数据库连接信息、参数开关等），实现 **配置复用** 与 **环境隔离**。

---

### 2.1 基本语法

```xml
<properties>
  <property name="username" value="root"/>
  <property name="password" value="123456"/>
</properties>
```

然后在其它地方使用：

```xml
<property name="username" value="${username}"/>
```

---

### 2.2 用法：引入外部配置文件（推荐）

通常通过 `resource` 或 `url` 引入 `.properties` 文件，最常用于加载数据库连接参数：

#### 示例一：引入资源文件（classpath 中）

```xml
<properties resource="db.properties"/>
```

比如 `db.properties` 内容如下：

```properties
db.driver=com.mysql.cj.jdbc.Driver
db.url=jdbc:mysql://localhost:3306/test
db.username=root
db.password=123456
```

然后在配置中使用：

```xml
<environment id="dev">
    <transactionManager type="JDBC"/>
    <dataSource type="POOLED">
        <property name="driver" value="${db.driver}"/>
        <property name="url" value="${db.url}"/>
        <property name="username" value="${db.username}"/>
        <property name="password" value="${db.password}"/>
    </dataSource>
</environment>
```

---

#### 示例二：引入 URL

```xml
<properties url="file:///etc/mybatis/db.properties"/>
```

---

#### 示例三：**混合使用**（本地优先生效）

```xml
<properties resource="db.properties">
  <property name="db.username" value="override-root"/>
</properties>
```


---

### 2.3 配置优先级

如果一个属性在不只一个地方进行了配置，那么，MyBatis 将按照下面的顺序来加载：

- 首先读取在 properties 元素体内指定的属性。
- 然后根据 properties 元素中的 resource 属性读取类路径下属性文件，或根据 url 属性指定的路径读取属性文件，并覆盖之前读取过的同名属性。
- 最后读取作为方法参数传递的属性，并覆盖之前读取过的同名属性。


## 3. settings(设置)



<settings> 是 MyBatis 的核心配置之一，用于控制其全局行为开关，如缓存、延迟加载、驼峰映射等。

### 配置参考

[官方文档](https://mybatis.org/mybatis-3/zh_CN/configuration.html#settings)

```xml
<settings>
  <!-- 是否启用二级缓存（全局开关） -->
  <setting name="cacheEnabled" value="true"/>
  
  <!-- 是否启用延迟加载 -->
  <setting name="lazyLoadingEnabled" value="true"/>
  
  <!-- 激进懒加载：true 表示只要访问一个懒加载属性就加载所有；false 表示按需加载 -->
  <setting name="aggressiveLazyLoading" value="true"/>
  
  <!-- 是否使用列别名（select column AS alias） -->
  <setting name="useColumnLabel" value="true"/>
  
  <!-- 是否使用 JDBC 的 getGeneratedKeys 方法获取数据库自增主键 -->
  <setting name="useGeneratedKeys" value="false"/>
  
  <!-- 自动映射行为：PARTIAL 表示只映射未显式指定的字段 -->
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  
  <!-- 遇到未知列时的自动映射行为：WARNING 表示发出警告 -->
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  
  <!-- 默认的 SQL 执行器类型：SIMPLE（每次创建新 Statement） -->
  <setting name="defaultExecutorType" value="SIMPLE"/>
  
  <!-- 默认的 SQL 查询超时时间（单位：秒） -->
  <setting name="defaultStatementTimeout" value="25"/>
  
  <!-- JDBC 的 fetchSize 设置，用于控制一次性抓取的数据量（可调优） -->
  <setting name="defaultFetchSize" value="100"/>
  
  <!-- 是否启用 RowBounds 分页的安全模式 -->
  <setting name="safeRowBoundsEnabled" value="false"/>
  
  <!-- 是否允许使用默认的 ResultHandler 处理结果集 -->
  <setting name="safeResultHandlerEnabled" value="true"/>
  
  <!-- 是否将下划线转为驼峰命名，如 user_id → userId -->
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  
  <!-- 一级缓存作用域：SESSION（默认），表示同一个 SqlSession 内共享缓存 -->
  <setting name="localCacheScope" value="SESSION"/>
  
  <!-- null 值对应的 JDBC 类型 -->
  <setting name="jdbcTypeForNull" value="OTHER"/>
  
  <!-- 会触发懒加载的 Java 方法列表 -->
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
  
  <!-- 默认的脚本语言驱动，一般是 XML 标签驱动 -->
  <setting name="defaultScriptingLanguage" value="org.apache.ibatis.scripting.xmltags.XMLLanguageDriver"/>
  
  <!-- 枚举类型默认使用的 TypeHandler -->
  <setting name="defaultEnumTypeHandler" value="org.apache.ibatis.type.EnumTypeHandler"/>
  
  <!-- 结果集中即使字段值为 null，也调用相应的 setter 方法 -->
  <setting name="callSettersOnNulls" value="false"/>
  
  <!-- 查询结果为空行时是否返回对象实例而不是 null -->
  <setting name="returnInstanceForEmptyRow" value="false"/>
  
  <!-- 日志前缀，用于区分输出 -->
  <setting name="logPrefix" value="exampleLogPreFix_"/>
  
  <!-- 日志实现，可选：SLF4J、LOG4J、LOG4J2、JDK_LOGGING、COMMONS_LOGGING、STDOUT_LOGGING、NO_LOGGING -->
  <setting name="logImpl" value="SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING"/>
  
  <!-- 指定 MyBatis 生成代理类使用的库：CGLIB 或 JAVASSIST -->
  <setting name="proxyFactory" value="CGLIB | JAVASSIST"/>
  
  <!-- 自定义的 VFS 实现类（可用于资源加载） -->
  <setting name="vfsImpl" value="org.mybatis.example.YourselfVfsImpl"/>
  
  <!-- 是否使用方法参数的实际名称（Java 8+ 支持，无需 @Param 注解） -->
  <setting name="useActualParamName" value="true"/>
  
  <!-- 指定 Configuration 对象的工厂类，可用于自定义加载配置 -->
  <setting name="configurationFactory" value="org.mybatis.example.ConfigurationFactory"/>
</settings>

```


## 4. typeAliases(类型别名)

typeAliases 是 MyBatis 配置文件（mybatis-config.xml）中的一个可选配置项，用于给 Java 类型定义 简短别名，让 Mapper XML 映射文件更简洁可读，避免频繁书写全限定类名（如 com.example.domain.User）。

### 示例配置

```xml

<typeAliases>
  <!-- 指定单个类型的别名 -->
  <typeAlias type="com.example.domain.User" alias="User"/>

  <!-- 指定包名，MyBatis 会自动为该包下所有类注册别名（默认类名小写作为别名） -->
  <package name="com.example.domain"/>
</typeAliases>

```

### 注解配置

```java
@Alias("myUser")
public class User {
    ...
}

```

## 5. typeHandlers(类型处理器)

`TypeHandler` 是 MyBatis 的一个扩展点，它实现了 Java 类型与 JDBC 类型之间的映射：

* Java → JDBC：设置参数（`PreparedStatement.setXXX()`）
* JDBC → Java：读取结果（`ResultSet.getXXX()`）

MyBatis 内置了常见类型的处理器，例如 `IntegerTypeHandler`、`StringTypeHandler`、`EnumTypeHandler` 等，也支持自定义。

---

### 5.1 配置示例

```xml
<typeHandlers>
  <!-- 注册单个类型处理器 -->
  <typeHandler javaType="com.example.MyEnum" jdbcType="VARCHAR"
               handler="com.example.MyEnumTypeHandler" />

  <!-- 自动扫描包，批量注册 -->
  <package name="com.example.handler"/>
</typeHandlers>
```

---


### 5.2 `<typeHandler>`

```xml
<typeHandler
    javaType="Java类型"
    jdbcType="JDBC类型"
    handler="TypeHandler实现类"/>
```

* `javaType`: 绑定的 Java 类型（可省略，若 handler 使用泛型参数）
* `jdbcType`: 对应的 JDBC 类型（如 `VARCHAR`, `INTEGER` 等）
* `handler`: 自定义的 `TypeHandler` 实现类

当 `javaType` 与 `jdbcType` 同时指定时，只有类型全匹配时才使用该处理器。

---

### 5.3 `<package>`

```xml
<package name="com.example.handler"/>
```

* 自动扫描该包下所有实现了 `TypeHandler` 接口的类并注册
* 注册时会自动解析泛型或注解 `@MappedTypes` 和 `@MappedJdbcTypes`

---

### 5.4 示例：枚举类处理器

```java
public enum Status {
    ENABLED, DISABLED;
}

@MappedTypes(Status.class)
@MappedJdbcTypes(JdbcType.VARCHAR)
public class StatusTypeHandler implements TypeHandler<Status> {
    @Override
    public void setParameter(PreparedStatement ps, int i, Status status, JdbcType jdbcType) throws SQLException {
        ps.setString(i, status.name());
    }

    @Override
    public Status getResult(ResultSet rs, String columnName) throws SQLException {
        return Status.valueOf(rs.getString(columnName));
    }

    // 其他 getResult 重载略
}
```

对应 XML 配置：

```xml
<typeHandlers>
  <typeHandler handler="com.example.handler.StatusTypeHandler"/>
</typeHandlers>
```

---

## 6. objectFactory(对象工厂)

在 MyBatis 中，<objectFactory> 是用来自定义 对象创建逻辑 的配置标签。MyBatis 默认使用反射机制（通过默认的 DefaultObjectFactory）来实例化结果对象，但如果有特殊的构造方式（如：依赖注入、构造函数带参数、缓存对象池等），就可以通过 <objectFactory> 配置来自定义这一过程。

### 示例

```java

// ExampleObjectFactory.java
public class ExampleObjectFactory extends DefaultObjectFactory {
  @Override
  public <T> T create(Class<T> type) {
    return super.create(type);
  }

  @Override
  public <T> T create(Class<T> type, List<Class<?>> constructorArgTypes, List<Object> constructorArgs) {
    return super.create(type, constructorArgTypes, constructorArgs);
  }

  @Override
  public void setProperties(Properties properties) {
    super.setProperties(properties);
  }

  @Override
  public <T> boolean isCollection(Class<T> type) {
    return Collection.class.isAssignableFrom(type);
  }}
```

```xml
<!-- mybatis-config.xml -->
<objectFactory type="org.mybatis.example.ExampleObjectFactory">
  <property name="someProperty" value="100"/>
</objectFactory>
```



## 7. plugins(插件)

在 MyBatis 中，`<plugins>` 用于配置 **插件（Interceptor）**，是 MyBatis 提供的强大扩展机制，可以实现对 MyBatis 核心行为的“切面式”拦截，比如拦截执行 SQL、参数处理、结果集处理等。

---

### 7.1 插件的本质：Interceptor 拦截器

插件必须实现 MyBatis 的接口：

```java
org.apache.ibatis.plugin.Interceptor
```

MyBatis 允许拦截以下 4 种类型的核心对象的方法：

| 拦截目标               | 接口类型         | 常用拦截方法                        |
| ------------------ | ------------ | ----------------------------- |
| `Executor`         | SQL 执行器      | `query()`, `update()`         |
| `StatementHandler` | 处理 SQL 语句的对象 | `prepare()`, `parameterize()` |
| `ParameterHandler` | 处理参数对象       | `setParameters()`             |
| `ResultSetHandler` | 处理查询结果       | `handleResultSets()`          |

---

### 7.2 配置结构

```xml
<plugins>
  <plugin interceptor="com.example.MyInterceptor">
    <property name="someConfig" value="someValue"/>
  </plugin>
</plugins>
```

* `interceptor`: 自定义拦截器的类名，必须实现 `org.apache.ibatis.plugin.Interceptor`
* `<property>`：可选的配置项，通过拦截器的 `setProperties(Properties)` 注入

---

### 7.3 插件示例

#### 实现 Interceptor 接口

```java
@Intercepts({
  @Signature(type = Executor.class, method = "query", args = {
    MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class
  })
})
public class MyInterceptor implements Interceptor {

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        System.out.println("Before SQL query...");
        Object result = invocation.proceed(); // 执行原方法
        System.out.println("After SQL query...");
        return result;
    }

    @Override
    public Object plugin(Object target) {
        // 生成代理对象
        return Plugin.wrap(target, this);
    }

    @Override
    public void setProperties(Properties properties) {
        System.out.println("插件参数：" + properties);
    }
}
```

#### 配置 XML

```xml
<plugins>
  <plugin interceptor="com.example.MyInterceptor">
    <property name="logLevel" value="DEBUG"/>
  </plugin>
</plugins>
```

---

## 8. environments(环境配置)

在 MyBatis 中，`<environments>` 标签用于配置 **多个运行环境（environment）**，比如开发环境、测试环境、生产环境等，每个环境都包含自己的事务管理器和数据源配置。

---

### 8.1 配置示例

```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC"/>
    <dataSource type="POOLED">
      <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
      <property name="url" value="jdbc:mysql://localhost:3306/test"/>
      <property name="username" value="root"/>
      <property name="password" value="123456"/>
    </dataSource>
  </environment>

  <environment id="production">
    <transactionManager type="MANAGED"/>
    <dataSource type="JNDI">
      <property name="data_source" value="java:comp/env/jdbc/ProdDB"/>
    </dataSource>
  </environment>
</environments>
```

---


### 8.2 `<environments default="development">`

* `default`：指定默认环境 ID，当构建 `SqlSessionFactory` 时使用。可以在程序中通过传参切换：

  ```java
  SqlSessionFactoryBuilder.build(inputStream, "production");
  ```

---

### 8.3`<environment>` 子标签

```xml
<environment id="开发环境唯一标识">
  ...
</environment>
```

* `id`：环境名称，必须唯一，用于在多个环境中选择当前激活的。

---

### 8.4 `<transactionManager>` 事务管理器

```xml
<transactionManager type="JDBC"/> <!-- 或 MANAGED -->
```

| 类型        | 说明                                                       |
| --------- | -------------------------------------------------------- |
| `JDBC`    | 使用 JDBC 提供的事务控制（通过 `Connection.commit()` 和 `rollback()`） |
| `MANAGED` | 让外部容器（如 Spring）管理事务，MyBatis 不会关闭连接（常配合 Spring 使用）        |

---

### 8.5 `<dataSource>` 数据源配置

```xml
<dataSource type="POOLED">
  <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
  ...
</dataSource>
```

| 类型         | 说明                                     |
| ---------- | -------------------------------------- |
| `POOLED`   | 使用 MyBatis 内置数据库连接池（默认推荐）              |
| `UNPOOLED` | 每次操作都新建连接（性能差）                         |
| `JNDI`     | 使用应用服务器配置的数据源（通常用于 Tomcat、WebSphere 等） |

#### 常见属性（以 `POOLED` 为例）：

| 属性名        | 说明        |
| ---------- | --------- |
| `driver`   | JDBC 驱动类名 |
| `url`      | JDBC URL  |
| `username` | 数据库用户名    |
| `password` | 密码        |

---



## 9. databaseIdProvider(数据库厂商标识)

在 MyBatis 中，`<databaseIdProvider>` 是用于识别不同数据库厂商的配置标签，它的主要作用是 **实现 SQL 的数据库差异化支持**，允许你根据数据库类型自动加载对应 SQL 语句。

---

### 9.1 配置示例

```xml
<databaseIdProvider type="DB_VENDOR">
  <property name="MySQL" value="mysql"/>
  <property name="Oracle" value="oracle"/>
  <property name="PostgreSQL" value="postgresql"/>
</databaseIdProvider>
```

---

### 9.2 `type`

目前内置支持：

| 类型          | 说明                                                                                 |
| ----------- | ---------------------------------------------------------------------------------- |
| `DB_VENDOR` | MyBatis 内部通过 `DatabaseMetaData#getDatabaseProductName()` 获取数据库厂商名，并映射成指定的 `value`。 |

可以实现自定义的 `DatabaseIdProvider` 接口来扩展，比如读取配置文件或环境变量。

---

### 9.3 `<property name="数据库厂商名" value="自定义别名"/>`

这些是从 JDBC 的 `DatabaseMetaData#getDatabaseProductName()` 获取的厂商名称（区分大小写），例如：

| `getDatabaseProductName()` 返回值 | 常见数据库      |
| ------------------------------ | ---------- |
| `MySQL`                        | MySQL      |
| `Oracle`                       | Oracle     |
| `PostgreSQL`                   | PostgreSQL |
| `Microsoft SQL Server`         | SQL Server |

---

### 9.4 使用场景示例

可以在 SQL 映射文件中根据 `databaseId` 编写多套 SQL：

```xml
<!-- 适用于 MySQL -->
<select id="selectUser" databaseId="mysql">
  SELECT * FROM user LIMIT 1
</select>

<!-- 适用于 Oracle -->
<select id="selectUser" databaseId="oracle">
  SELECT * FROM (SELECT u.*, ROWNUM rn FROM user u WHERE ROWNUM <= 1)
</select>

<!-- 通用 SQL -->
<select id="selectUser">
  SELECT * FROM user
</select>
```

> MyBatis 会根据当前环境识别到的 `databaseId` 自动选择最合适的语句。

---


## 10. mappers(映射文件)

在 MyBatis 的配置文件（`mybatis-config.xml`）中，`<mappers>` 标签用于指定 **Mapper 接口及其 XML 映射文件** 的加载方式。MyBatis 通过这些配置找到对应的 SQL 映射逻辑，是配置的核心部分之一。


### 10.1 **使用 `<mapper resource="..."/>`**（推荐 XML 配置方式）

```xml
<mappers>
  <mapper resource="mapper/UserMapper.xml"/>
</mappers>
```

* 加载 classpath 中的 `mapper/UserMapper.xml` 文件。
* `resource` 路径为 **相对于 classpath 根目录的路径**。

### 10.2 **使用 `<mapper url="..."/>`**（不常用）

```xml
<mappers>
  <mapper url="file:///C:/mappers/UserMapper.xml"/>
</mappers>
```

* 从本地文件系统或网络路径加载 mapper 文件。
* **很少使用，一般用于特殊场景**（如动态加载 SQL 文件）。

### 10.3 **使用 `<mapper class="..."/>`**（推荐注解方式）

```xml
<mappers>
  <mapper class="com.example.mapper.UserMapper"/>
</mappers>
```

* 适用于使用 **注解（@Select、@Insert 等）方式定义 SQL** 的 Mapper 接口。
* 要求接口和 XML 映射文件一致时（如配合 `MapperScannerConfigurer`）不要使用此方式。

### 10.4 **使用 `<package name="..."/>`**（批量扫描）

```xml
<mappers>
  <package name="com.example.mapper"/>
</mappers>
```

* 会扫描该包下的所有接口（`.class` 文件）并注册为 Mapper。
* 要求：

  * Mapper 接口和 XML（如果有）**文件名相同**。
  * 都放在相同的 `classpath` 路径下（通常放在 `resources/mapper` 中）。

---




