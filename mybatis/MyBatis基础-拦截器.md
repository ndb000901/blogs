# MyBatis基础-拦截器


## 1. 插件本质：Interceptor 拦截器

MyBatis 插件基于 Java 的 **责任链模式（Interceptor Chain）**，通过实现 `org.apache.ibatis.plugin.Interceptor` 接口并使用注解声明拦截哪些对象方法。

---

## 2. 能拦截核心对象

MyBatis 插件只能拦截以下四种对象的 **特定方法**：

| 拦截对象类型             | 可拦截的方法                                                            |
| ------------------ | ----------------------------------------------------------------- |
| `Executor`         | `update`、`query`、`flushStatements` 等                              |
| `ParameterHandler` | `getParameterObject()`、`setParameters(PreparedStatement)`         |
| `ResultSetHandler` | `handleResultSets`、`handleOutputParameters`                       |
| `StatementHandler` | `prepare(Connection,int)`、`parameterize`、`batch`、`update`、`query` |

这些都是 MyBatis 核心执行过程的关键环节。

---

## 3. 示例：打印 SQL 的插件

### 插件实现类：

```java
@Intercepts({
  @Signature(
    type = StatementHandler.class,
    method = "prepare",
    args = {Connection.class, Integer.class}
  )
})
public class SqlPrintInterceptor implements Interceptor {

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        StatementHandler statementHandler = (StatementHandler) PluginUtils.realTarget(invocation.getTarget());
        BoundSql boundSql = statementHandler.getBoundSql();
        System.out.println("==> SQL: " + boundSql.getSql());
        return invocation.proceed(); // 放行
    }

    @Override
    public Object plugin(Object target) {
        return Plugin.wrap(target, this); // 包装目标对象
    }

    @Override
    public void setProperties(Properties properties) {
        // 可读取配置参数
    }
}
```

---

## 4. 插件结构说明

### `@Intercepts` 注解

定义插件要拦截的目标方法，必须注明：

* `type`: 拦截的接口类型，如 `Executor`、`StatementHandler`
* `method`: 要拦截的方法名
* `args`: 方法参数类型数组

### `intercept` 方法

这是核心逻辑，你可以在执行前、后做增强处理，也可以完全替换原逻辑。

### `plugin` 方法

用于将插件包装到目标对象中。

### `setProperties` 方法

支持从 MyBatis 配置文件读取参数（可选）。

---

## 5. 插件注册方式

### 方法一：配置到 `mybatis-config.xml`

```xml
<plugins>
  <plugin interceptor="com.example.interceptor.SqlPrintInterceptor">
    <property name="showSql" value="true"/>
  </plugin>
</plugins>
```

### 方法二：Spring Boot 自动配置

Spring Boot 中使用 `@Component` 注解并注册到 Spring 容器，MyBatis 会自动发现：

```java
@Component
public class SqlPrintInterceptor implements Interceptor { ... }
```

---




