# Docker 数据卷

**Volume（数据卷）** 是 Docker 提供的一种专门用于**持久化容器数据**的机制，由 Docker 管理并保存在主机的文件系统中（默认在 `/var/lib/docker/volumes/` 目录下）。

相比直接将目录挂载到容器，Volume 更适合跨平台、更易于备份、迁移与隔离。

---

## 1. Volume 的类型

| 类型 | 描述 |
|------|------|
| **匿名卷** | 自动创建、无特定名称，只能通过容器 ID 引用 |
| **具名卷** | 用户显式命名，可以跨容器共享 |
| **绑定挂载（Bind Mount）** | 将主机目录直接挂载到容器中，适用于开发调试 |
| **tmpfs 挂载** | 数据存储在内存中，容器停止就会丢失（高性能临时数据） |

---

## 2. 使用方式详解

### 2.1 匿名卷（自动创建）

```bash

docker run -v /data busybox
```

- 容器内部的 `/data` 目录将挂载一个匿名卷。
- Docker 自动管理，无需命名，但不易维护。

---

### 2.2 具名卷（推荐）

```bash

docker volume create mydata

docker run -v mydata:/data busybox
```

- `mydata` 是卷名，可以复用、共享给多个容器。
- 推荐用于生产环境。

---

### 2.3 Bind Mount（主机目录挂载）

```bash

docker run -v /host/path:/container/path busybox
```

- `/host/path` 是主机路径，直接映射。
- 优点：开发调试方便。
- 缺点：不可移植、主机路径变化容易出错。

---

### 2.4 tmpfs（内存挂载）

```bash

docker run --tmpfs /tmp busybox
```

- 用于极高性能场景（如缓存）。
- 容器停止，数据即失效。

---

## 3. Volume 命令

| 命令 | 说明 |
|------|------|
| `docker volume ls` | 列出所有卷 |
| `docker volume create NAME` | 创建数据卷 |
| `docker volume inspect NAME` | 查看卷的详细信息 |
| `docker volume rm NAME` | 删除卷（必须未被使用） |
| `docker volume prune` | 清除未使用的卷 |

---

