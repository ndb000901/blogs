# Jenkins 安装教程

## 1. 下载

[下载页面](https://www.jenkins.io/download/)

```bash

wget https://get.jenkins.io/war-stable/2.492.3/jenkins.war
```

## 2. 启动

```bash

java -jar jenkins.war --httpPort=28080
```

### 使用输出code解锁 Jenkins

```text

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

732ca4e4f75c4207b1c7ef599438f4f2

This may also be found at: /home/hello/.jenkins/secrets/initialAdminPassword
```