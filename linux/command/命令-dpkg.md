# 命令dpkg 详解

dpkg 是 Debian 系统（包括 Ubuntu）用于管理 .deb 安装包的底层命令工具

它类似于：
- rpm（用于 Red Hat 系列）
- pacman（用于 Arch）
- apk（用于 Alpine）

而我们平常用的 apt install 实际上也是基于 dpkg 做了一层封装，它多了依赖解析、自动下载等功能。

## 常用命令

| 场景 | 命令                     |
|-------|------------------------|
| 安装 `.deb` 包 | `sudo dpkg -i xxx.deb` |
| 卸载已安装包 | `sudo dpkg -r 包名`      |
| 完全删除包及配置 | `sudo dpkg -P 包名`      |
| 查看已安装包 | `dpkg -l`              |
| 查询某个包是否安装 | `dpkg -l \| grep 包名`   |
| 查询某个文件属于哪个包 | `dpkg -S 路径`           |
| 查看包的详细信息 | `dpkg -s 包名`           |
| 查看 .deb 包的内容 | `dpkg -c xxx.deb`      |
| 查看 .deb 包的控制信息 | `dpkg -I xxx.deb`      |
