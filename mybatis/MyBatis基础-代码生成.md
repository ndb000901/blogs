# MyBatis基础-代码生成

## 1. 工具

[文档地址](https://github.com/itfsw/mybatis-generator-plugin)

[项目参考](https://github.com/ndb000901/spring-demo/tree/main/mybatis-tools)

## 2. `pom.xml` 配置

```xml

<properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <mysql.driver.version>5.1.49</mysql.driver.version>
    <mybatis.generator.plugin.version>1.3.10</mybatis.generator.plugin.version>
</properties>
<build>
<plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
            <annotationProcessorPaths>
                <path>
                    <groupId>org.projectlombok</groupId>
                    <artifactId>lombok</artifactId>
                </path>
            </annotationProcessorPaths>
        </configuration>
    </plugin>
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
            <excludes>
                <exclude>
                    <groupId>org.projectlombok</groupId>
                    <artifactId>lombok</artifactId>
                </exclude>
            </excludes>
        </configuration>
    </plugin>

    <!-- mybatis-generator 自动代码插件 -->
    <plugin>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-maven-plugin</artifactId>
        <version>1.3.7</version>
        <configuration>
            <!-- 配置文件 -->
            <configurationFile>src/main/resources/mybatis-generator.xml</configurationFile>
            <!-- 允许移动和修改 -->
            <verbose>true</verbose>
            <overwrite>true</overwrite>
        </configuration>
        <dependencies>
            <!-- jdbc 依赖 -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.driver.version}</version>
            </dependency>
            <dependency>
                <groupId>com.itfsw</groupId>
                <artifactId>mybatis-generator-plugin</artifactId>
                <version>${mybatis.generator.plugin.version}</version>
            </dependency>
        </dependencies>
    </plugin>
</plugins>
</build>
```

## 3. `device.sql`

```sql
CREATE TABLE `t_device` (
  `Findex` int(11) NOT NULL COMMENT '主键',
  `Fdevice_id` varchar(32) NOT NULL DEFAULT '' COMMENT '设备id',
  `Fdevice_name` varchar(128) NOT NULL DEFAULT '' COMMENT '设备名称',
  `Fimei1` varchar(64) NOT NULL DEFAULT '' COMMENT '设备imei标识1',
  `Fimei2` varchar(64) NOT NULL DEFAULT '' COMMENT '设备imei标识2',
  `Ficcid1` varchar(64) NOT NULL DEFAULT '' COMMENT 'sim卡1 iccid',
  `Ficcid2` varchar(64) NOT NULL DEFAULT '' COMMENT 'sim卡2 iccid',
  `Fphone1` varchar(32) NOT NULL DEFAULT '' COMMENT 'sim卡1 号码',
  `Fphone2` varchar(32) NOT NULL DEFAULT '' COMMENT 'sim卡2 号码',
  `Fzzy_device_status` char(2) NOT NULL DEFAULT '0' COMMENT '设备状态，取值为：\r\n1=正常，3=预注册，4=已丢失，5=待删除，6=闲置，7=已擦除，8=待擦除，9=已删除(仅用于接口，实际设备无此状态)',
  `Fstatus` tinyint(4) NOT NULL DEFAULT '0' COMMENT '系统设备状态，0-已停用,1-启用,2-失效,',
  `Ftenant_id` int(11) NOT NULL DEFAULT '0' COMMENT '所属租户ID',
  `Fowner` varchar(128) NOT NULL DEFAULT '' COMMENT '设备拥有者,系统用户账号(英文名)',
  `Foperator` varchar(128) NOT NULL DEFAULT '' COMMENT '操作人',
  `Fversion` int(11) NOT NULL DEFAULT '0' COMMENT '版本号',
  `Fcreate_time` datetime NOT NULL DEFAULT '1970-01-01 00:00:00' COMMENT '记录创建时间',
  `Fmodify_time` datetime NOT NULL DEFAULT '1970-01-01 00:00:00' COMMENT '记录修改时间'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='设备列表';
```

## 4. `mybatis-generator.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!-- 引入配置文件 -->
    <!-- 可以统一配置需要的信息 -->
    <!--<properties resource="application.properties"/>-->
    <!--数据库驱动jar, 指定本地maven仓库的mysql-connector-java-*.*.**.jar -->
    <classPathEntry
            location="/Users/hello/.m2/repository/mysql/mysql-connector-java/5.1.49/mysql-connector-java-5.1.49.jar"/>
    <context id="DB2Tables" targetRuntime="MyBatis3">

        <!-- 查询单条数据插件 -->
        <plugin type="com.itfsw.mybatis.generator.plugins.SelectOneByExamplePlugin"/>
        <!-- 数据Model链式构建插件 -->
        <plugin type="com.itfsw.mybatis.generator.plugins.ModelBuilderPlugin"/>
        <!-- Example Criteria 增强插件 -->
        <plugin type="com.itfsw.mybatis.generator.plugins.ExampleEnhancedPlugin">
            <!-- 是否支持已经过时的andIf方法（推荐使用when代替），默认支持 -->
            <property name="enableAndIf" value="true"/>
        </plugin>
        <!-- 批量插入插件 -->
        <plugin type="com.itfsw.mybatis.generator.plugins.BatchInsertPlugin">
            <!--
            开启后可以实现官方插件根据属性是否为空决定是否插入该字段功能
            ！需开启allowMultiQueries=true多条sql提交操作，所以不建议使用！插件默认不开启
            -->
            <property name="allowMultiQueries" value="false"/>
        </plugin>
        <!-- 数据Model属性对应Column获取插件 -->
        <plugin type="com.itfsw.mybatis.generator.plugins.ModelColumnPlugin"/>
        <!-- 存在即更新插件 -->
        <plugin type="com.itfsw.mybatis.generator.plugins.UpsertPlugin">
            <!--
            支持upsertByExample，upsertByExampleSelective操作
            ！需开启allowMultiQueries=true多条sql提交操作，所以不建议使用！插件默认不开启
            -->
            <property name="allowMultiQueries" value="false"/>
            <!--
            开启批量功能，支持batchUpsert,batchUpsertWithBLOBs,batchUpserSelective
            ！这几个方法中无法支持IncrementsPlugin的方法！插件默认不开启
            -->
            <property name="allowBatchUpsert" value="fasle"/>
        </plugin>

        <!--去除注释  -->
        <commentGenerator>
            <!-- 是否取消注释 -->
            <property name="suppressAllComments"
                      value="false"/>
            <!-- <property name="suppressDate" value="true" />  是否生成注释代时间戳 -->
        </commentGenerator>
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://192.168.43.242:13306/telesale"
                        userId="root"
                        password="root"/>
        <!--默认false   Java type resolver will always use java.math.BigDecimal if the database column is of type DECIMAL or NUMERIC.         -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!--生成实体类 指定包名 以及生成的地址 （可以自定义地址，但是路径不存在不会自动创建  使用Maven生成在target目录下，会自动创建） -->
        <!--文件存放路径(targetProject) targetProject可以指定具体的路径,如./src/main/java， 也可以使用"MAVEN"来自动生成，这样生成的代码会在target/generatord-source目录下-->
        <!-- 用maven自动生成的话，生成之后剪切到对应的项目目录下 -->
        <javaModelGenerator targetPackage="com.demo.domain" targetProject="MAVEN">
            <property name="enableSubPackages" value="false"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!--生成SQLMAP文件 -->
        <!-- mapper是dao 映射文件，项目约定放到 src/main/resource 目录下 -->
        <sqlMapGenerator targetPackage="com.demo.mappers" targetProject="MAVEN">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <!--生成Dao文件 可以配置 type="XMLMAPPER"生成xml的dao实现  context id="DB2Tables" 修改targetRuntime="MyBatis3"  -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.demo.dao" targetProject="MAVEN">
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>


        <!--关联表-->
        <table tableName="t_device" domainObjectName="Device">
            <!--去掉字段的 F 前缀-->
            <columnRenamingRule searchString="^F" replaceString=""/>
        </table>
    </context>
</generatorConfiguration>

```

## 5. `application.properties`

```properties
spring.application.name=dao-demo

mybatis.mapper-locations=classpath:mappers/*.xml
spring.datasource.url=jdbc:mysql://192.168.43.242:13306/telesale?useSSL=false&useUnicode=true&characterEncoding=utf8
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

## 6. 生成代码

在模块目录下执行以下命令即可生成代码(target目录)：

```bash

mvn org.mybatis.generator:mybatis-generator-maven-plugin:1.3.7:generate

```