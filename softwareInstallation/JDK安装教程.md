# JDK安装教程-linux

## 1.下载JDK

[下载地址](https://www.oracle.com/java/technologies/downloads/)

```bash

wget xxxxxxxxx
```

## 2.解压

```bash

tar -zxvf xxxxxx
```

## 3.设置环境变量

```bash

# 1.编辑~/.zshrc文件(根据自己的环境配置)
vim ~/.zshrc

# 2.添加环境变量(写入到～/.zshrc文件)

## java
JAVA_HOME=/home/hello/local/jdk/jdk1.8.0_431
CLASSPATH=$JAVA_HOME/lib/
PATH=$PATH:$JAVA_HOME/bin
export PATH JAVA_HOME CLASSPATH

# 3.刷新

source ~/.zshrc
```

## 4.验证

```bash

java --version
```

### 输出

```bash

java version "1.8.0_431"
Java(TM) SE Runtime Environment (build 1.8.0_431-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.431-b10, mixed mode)
```