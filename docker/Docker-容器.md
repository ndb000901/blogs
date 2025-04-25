# Docker 容器

> Docker 容器是基于镜像启动的、**带有独立进程空间和文件系统的轻量级运行时环境**。

可以把它理解为：
- 镜像是“操作系统的快照”
- 容器是“镜像运行后的进程实例”

一个容器：
- 基于镜像启动（不可变）
- 自带隔离的网络、进程、文件系统
- 是 Linux 容器技术（如 namespaces、cgroups）+ Docker 引擎包装出来的产物

---

## 1. Docker 容器底层原理

容器通过 Linux 的几项核心技术实现隔离和资源控制：

| 技术 | 功能 |
|------|------|
| **Namespaces** | 进程、网络、挂载、IPC、UTS 等隔离（比如 PID 隔离） |
| **cgroups** | 控制资源使用（CPU、内存、磁盘 I/O 等） |
| **Union FS** | 支持镜像层叠结构 |
| **chroot** / **mount** | 文件系统根目录隔离 |
| **RunC** | Docker 最底层调用的 OCI 容器运行时（真正启动容器进程） |

---

## 2. 容器与虚拟机的区别

| 对比项 | 容器 | 虚拟机 |
|--------|------|--------|
| 启动速度 | 秒级 | 分钟级 |
| 性能开销 | 低 | 高（有 hypervisor） |
| 文件系统 | 镜像叠加+写层 | 独立 OS |
| 隔离级别 | 进程级 | 内核级 |
| 使用场景 | 微服务、CI/CD、开发 | 安全隔离、多系统环境 |

---

## 3. 容器命令

### 3.1 启动与创建容器

```bash

docker run -it ubuntu bash         # 启动并进入交互式容器
docker run -d nginx                # 后台运行 nginx 容器
docker run --name web -p 8080:80 nginx  # 映射端口并命名容器
```

### 3.2 容器生命周期管理

```bash

docker ps                         # 列出运行中的容器
docker ps -a                      # 所有容器（含停止）
docker stop <容器名/ID>           # 停止容器
docker start <容器名/ID>          # 启动已停止容器
docker restart <容器名/ID>        # 重启容器
docker rm <容器名/ID>             # 删除容器
```

### 3.3 进入容器内部

```bash

docker exec -it mycontainer bash   # 进入已有容器并执行命令
docker attach mycontainer          # 附着到容器（慎用，CTRL+C 会关闭容器）
```

### 3.4 容器文件管理

```bash

docker cp file.txt container:/tmp     # 复制文件到容器
docker cp container:/etc/nginx/nginx.conf .  # 从容器复制出来
```

---

## 4. 容器挂载和数据持久化

容器默认是“临时”的，数据重启即消失。可以通过 **挂载卷** 保持数据：

```bash

docker run -v /host/data:/container/data ubuntu
```

也可以结合 `--mount` 更明确挂载类型、只读等选项。

---

## 5. 容器的网络模式

```bash

docker run --network bridge ...       # 默认：桥接网络
docker run --network host ...         # 宿主机网络（无隔离）
docker run --network none ...         # 完全无网络
docker network create ...             # 自定义网络
```

用 `docker network ls` / `inspect` 查看容器网络。

---

## 6. 查看容器信息与日志

```bash

docker inspect mycontainer         # 查看容器详情（JSON）
docker logs mycontainer            # 查看日志输出
docker top mycontainer             # 查看容器内进程
docker stats                       # 查看资源使用情况
```

---
