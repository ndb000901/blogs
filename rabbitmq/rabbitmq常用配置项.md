# rabbitmq 常用配置项

### 1. **节点配置**
RabbitMQ可以在分布式系统中运行，以下是相关的配置项：

- **`NODE_NAME`**:  
  配置RabbitMQ节点的名称，通常在单机模式下不需要更改，默认是`rabbit@hostname`，但在集群模式下，每个节点的名称需要唯一。
  ```bash
  NODE_NAME=rabbit@localhost
  ```

- **`NODE_PORT`**:  
  配置RabbitMQ的端口，默认为5672。
  ```bash
  NODE_PORT=5672
  ```

### 2. **日志配置**
RabbitMQ支持不同级别的日志记录，以下是一些常用的配置项：

- **`LOG_BASE`**:  
  指定日志文件保存的路径。默认为`/var/log/rabbitmq`。
  ```bash
  LOG_BASE=/var/log/rabbitmq
  ```

- **`LOG_FILE`**:  
  配置日志文件的名称，通常包括`rabbit`前缀。
  ```bash
  LOG_FILE=rabbit.log
  ```

- **`LOG_LEVEL`**:  
  设置日志的详细程度，常见的值有：`debug`, `info`, `warning`, `error`。默认是`info`。
  ```bash
  LOG_LEVEL=info
  ```

### 3. **内存与磁盘管理**
RabbitMQ的内存和磁盘配置对于高负载系统非常重要，以下是几个常用的配置项：

- **`VM_MEMORY_HIGH_WATERMARK`**:  
  设置内存高水位，用于限制内存使用的上限。默认值为`0.4`，即40%的物理内存。
  ```bash
  VM_MEMORY_HIGH_WATERMARK=0.4
  ```

- **`DISK_FREE_LIMIT`**:  
  设置磁盘空间的最小阈值。如果磁盘空间低于此值，RabbitMQ将停止写入消息。默认值为`1GB`。
  ```bash
  DISK_FREE_LIMIT=1GB
  ```

### 4. **连接与通道配置**
RabbitMQ支持多种连接与通道的配置，以保证性能和可靠性：

- **`TCP_LISTENER_OPTIONS`**:  
  配置TCP连接的选项，如`backlog`（连接排队）、`linger`（连接超时等待）等。
  ```bash
  TCP_LISTENER_OPTIONS={backlog, 128}
  ```

- **`CHANNEL_MAX`**:  
  设置每个连接上最大通道数。默认情况下是65535。
  ```bash
  CHANNEL_MAX=65535
  ```

- **`TCP_LISTENER_PORT`**:  
  设置默认的TCP监听端口，默认为`5672`。
  ```bash
  TCP_LISTENER_PORT=5672
  ```

### 5. **用户与权限配置**
RabbitMQ提供了非常详细的权限管理，以下是一些与用户和权限相关的配置项：

- **`DEFAULT_USER`**:  
  配置默认的管理员用户名，默认为`guest`。
  ```bash
  DEFAULT_USER=guest
  ```

- **`DEFAULT_PASS`**:  
  配置默认的管理员密码，默认为`guest`。
  ```bash
  DEFAULT_PASS=guest
  ```

- **`ACCESS_CONTROL`**:  
  控制对RabbitMQ管理接口的访问权限。
  ```bash
  ACCESS_CONTROL={allow, "localhost"}
  ```

### 6. **集群配置**
RabbitMQ可以配置成集群模式，以下是集群相关的配置项：

- **`CLUSTER_NAME`**:  
  设置RabbitMQ集群的名称。默认情况下是`rabbit@hostname`。
  ```bash
  CLUSTER_NAME=rabbit@mycluster
  ```

- **`CLUSTER_PARTITION_HANDLING`**:  
  配置当节点出现分区（partition）时的处理方式。可以选择`ignore`（忽略分区）、`pause_minority`（暂停少数节点）等。
  ```bash
  CLUSTER_PARTITION_HANDLING=pause_minority
  ```

### 7. **插件配置**
RabbitMQ通过插件扩展功能，以下是一些与插件相关的配置项：

- **`ENABLE_MANAGEMENT_PLUGIN`**:  
  是否启用RabbitMQ的Web管理插件，默认是`false`。启用后可以通过浏览器访问管理界面。
  ```bash
  ENABLE_MANAGEMENT_PLUGIN=true
  ```

- **`ENABLE_SHOVEL_PLUGIN`**:  
  启用shovel插件，用于跨RabbitMQ集群或节点复制消息。
  ```bash
  ENABLE_SHOVEL_PLUGIN=true
  ```

### 8. **AMQP协议设置**
RabbitMQ支持AMQP协议的一些定制设置：

- **`AMQP_VERSION`**:  
  设置AMQP协议的版本，通常是`0-9-1`。
  ```bash
  AMQP_VERSION=0-9-1
  ```

- **`DEFAULT_VHOST`**:  
  配置默认虚拟主机。虚拟主机可以用来隔离不同的应用或不同的权限配置。默认是`/`。
  ```bash
  DEFAULT_VHOST=/
  ```

### 9. **消息队列与交换机**
消息队列和交换机的配置项：

- **`DEFAULT_QUEUE_TTL`**:  
  设置默认队列的生命周期（TTL），即消息在队列中存活的时间。单位是毫秒。
  ```bash
  DEFAULT_QUEUE_TTL=60000  # 1 minute
  ```

- **`DEFAULT_EXCHANGE_TYPE`**:  
  设置默认交换机类型。可以是`direct`、`fanout`、`topic`等。
  ```bash
  DEFAULT_EXCHANGE_TYPE=direct
  ```

### 10. **SSL/TLS 配置**
RabbitMQ也支持SSL/TLS加密连接：

- **`SSL_LISTENER_OPTIONS`**:  
  配置SSL/TLS相关的选项，如证书路径、私钥路径等。
  ```bash
  SSL_LISTENER_OPTIONS={certfile, "/path/to/cert.pem"}, {keyfile, "/path/to/key.pem"}
  ```

- **`SSL_LISTENER_PORT`**:  
  配置SSL/TLS监听的端口，默认为`5671`。
  ```bash
  SSL_LISTENER_PORT=5671
  ```
