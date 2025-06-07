# istoreos 安装

## 1. 背景

用vmware虚拟机搭建`istoreos`做旁路由, 代理流量。

## 2. 下载

[wiki](https://github.com/istoreos/istoreos/wiki)

[下载页面](https://site.istoreos.com/firmware)

我下载x86_64的版本


## 3. 转换磁盘镜像文件格式


```bash

# ubuntu 安装 qume
sudo apt update
sudo apt install qemu-utils

# macos 安装qemu
brew install qemu

# 转换格式
qemu-img convert -f raw -O vmdk istoreos-24.10.0-2025051619-x86-64-squashfs-combined-efi.img istoreos-24.10.0-2025051619-x86-64-squashfs-combined-efi.vmdk
```

## 4. 创建虚拟机

- 操作系统：Linux
- Other Linux 3.x
- 使用已存在磁盘创建(`istoreos-24.10.0-2025051619-x86-64-squashfs-combined-efi.vmdk`)
- 网络桥接模式

## 5. 登录

配置：`vim /etc/config/network`



使用方法

默认IP http://192.168.100.1

默认密码：password

如果只有一个网口，默认的网口是 LAN，且DHCP自动获取 IP；如果大于一个网口，默认 eth0 是 WAN 口，其它都是 LAN。

如果在 LAN 口修改 IP，或者任何修改之后导致无法连接路由器，都会导致刚才的修改被回滚。所以要修改 LAN/WAN 口 IP，可以选择强制应用，保证修改肯定生效。


## 6. 参考配置

```conf
config interface 'loopback'
        option device 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fd35:6c04:4d92::/48'

config device
        option name 'br-lan'
        option type 'bridge'
        list ports 'eth0'
        list ports 'dheth0'

config interface 'lan'
        option device 'br-lan'
        option proto 'static'
        option ipaddr '192.168.43.110'
        option netmask '255.255.255.0'
        option gateway '192.168.43.1'
        option peerdns '0'
        list dns '8.8.8.8'
        list dns '1.1.1.1'
```

## 7. 插件

- [passwall2](https://github.com/xiaorouji/openwrt-passwall2)


## 8. 配置设备

将需要的设备网关配置为虚拟机的网关：`192.168.43.110`