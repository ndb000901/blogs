# Mysql基础-Mysql编译

## 1. 下载

```bash

wget -c https://dev.mysql.com/get/Downloads/MySQL-8.4/mysql-8.4.4.tar.gz
```

## 2. 解压

```bash

tar -zxvf mysql-8.4.4.tar.gz
```

## 3. 依赖

```bash

sudo apt update
sudo apt install -y build-essential cmake \
    libncurses5-dev libssl-dev libboost-all-dev \
    bison pkg-config libevent-dev \
    libaio-dev zlib1g-dev \
    libtirpc-dev \
    git

```

## 4. 编译

```bash

cd mysql-8.4.4

mkdir build

cd build
cmake ..

make -j 8
```