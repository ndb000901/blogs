# Maven安装教程

## 1.下载

[下载地址](https://maven.apache.org/download.cgi)

```bash

wget https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.zip
```

## 2.解压

```bash
unzip apache-maven-3.9.9-bin.zip
```

## 3.环境变量

```bash
# vim ~/.zshrc

export MAVEN_HOME=/home/hello/local/maven/apache-maven-3.9.9-bin
export PATH=$MAVEN_HOME/bin:$PATH

# 刷新
source ~/.zshrc
```

## 4.验证

```bash

mvn -v

# 输出
Apache Maven 3.9.9 (8e8579a9e76f7d015ee5ec7bfcdc97d260186937)
Maven home: /home/hello/local/maven/apache-maven-3.9.9
Java version: 1.8.0_441, vendor: Oracle Corporation, runtime: /home/hello/local/java/jdk1.8.0_441/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "5.15.0-130-generic", arch: "amd64", family: "unix"
```


