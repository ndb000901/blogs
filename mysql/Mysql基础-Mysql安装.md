# Mysql基础-Mysql安装

## 1. 下载

[下载页面](https://dev.mysql.com/downloads/mysql/)

```bash

wget -c wget -c https://dev.mysql.com/get/Downloads/MySQL-8.4/mysql-8.4.4-linux-glibc2.28-x86_64.tar.xz
```


## 2.解压

```bash

tar -xvf mysql-8.4.4-linux-glibc2.28-x86_64.tar.xz
```

## 3.添加环境变量

```bash

# vim ~/.zshrc
export PATH=$PATH:/home/hello/local/mysql-8.4.4/bin

# 刷新
source ~/.zshrc
```

