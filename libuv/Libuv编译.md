# Libuv 编译

## 1.环境

```shell
# lsb_release -a

No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.4 LTS
Release:	22.04
Codename:	jammy

# uname -a
Linux dev 5.15.0-122-generic #132-Ubuntu SMP Thu Aug 29 13:45:52 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux

# 源码
git clone https://github.com/libuv/libuv.git
git checkout v1.46.0


```

## 2.安装依赖

```shell
sudo apt-get update

# autoconf: 
# automake:
# libtool: 
sudo apt-get install autoconf automake libtool

```

## 3.编译

```shell
./autogen.sh
./configure --prefix=/home/hello/opensource/libuv/out
make -j 16
make install
```