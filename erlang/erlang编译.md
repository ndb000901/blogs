# erlang编译

## 1.下载

[下载页面](https://www.erlang.org/downloads)

```bash

wget -c https://github.com/erlang/otp/releases/download/OTP-27.3/otp_src_27.3.tar.gz
```

## 2.解压

```bash

tar -zxvf otp_src_27.3.tar.gz
mv otp_src_27.3 erlang-27.3
```

## 3.依赖

```bash
sudo apt-get install build-essential
sudo apt-get install libssl-dev
sudo apt-get install libncurses5-dev
```

## 4.编译

```bash
./configure --prefix=/home/hello/opensource/erlang-27.3/out

make -j 16
make install

```