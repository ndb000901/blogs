# maven 使用教程

## 1.安装

[安装教程](../softwareInstallation/Maven安装教程.md)

## 2.maven生命周期

[相关文档](./maven生命周期.md)

## 3.本地仓库配置

编辑`settings.xml` 文件

```xml
<localRepository>/path/to/local/repo</localRepository>
```

## 4.设置http代理

```xml

<settings>
  <proxies>
    <proxy>
      <id>my-http-proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>proxy.example.com</host>
      <port>8080</port>
      <username>your-username</username> <!-- 可选 -->
      <password>your-password</password> <!-- 可选 -->
      <nonProxyHosts>localhost|*.mycompany.com</nonProxyHosts> <!-- 可选 -->
    </proxy>
  </proxies>
</settings>

```

## 5.使用阿里源

[相关文档](https://developer.aliyun.com/mirror/maven)

```xml

<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>

```

## 6.远程仓库


## 7.创建一个项目

[示例](./maven创建工程.md)

# 相关资料

- [菜鸟教程](https://www.runoob.com/maven/maven-tutorial.html)

- [官方文档]()