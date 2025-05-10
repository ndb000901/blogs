# MyBatis基础-分页插件

## 1. 依赖

```bash

<!-- com.github.pagehelper为PageHelper类所在包名 -->
<plugin interceptor="com.github.pagehelper.PageInterceptor">
    <!-- 使用下面的方式配置参数，后面会有所有的参数介绍 -->
    <property name="param1" value="value1"/>
</plugin>
```


## 2. `mybatis-config.xml`

```xml
<!--
    plugins在配置文件中的位置必须符合要求，否则会报错，顺序如下:
    properties?, settings?,
    typeAliases?, typeHandlers?,
    objectFactory?,objectWrapperFactory?,
    plugins?,
    environments?, databaseIdProvider?, mappers?
-->
<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!-- 使用下面的方式配置参数，后面会有所有的参数介绍 -->
        <property name="param1" value="value1"/>
    </plugin>
</plugins>
```

## 3. 代码示例

```java

@Test
public void testPage() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    PageHelper.startPage(1, 1); // 第1页，每页2条
    List<User> users = mapper.getUsers();
    PageInfo<User> pageInfo = new PageInfo<>(users); // 封装分页信息
    System.out.println(pageInfo);
}
```
