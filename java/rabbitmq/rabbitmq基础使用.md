# rabbitmq 基础使用

## 1.依赖

```xml
    <dependencies>
        <dependency>
            <groupId>com.rabbitmq</groupId>
            <artifactId>amqp-client</artifactId>
            <version>5.24.0</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.18.2</version>
        </dependency>
    </dependencies>

```

## 2.`queueDeclare` 方法解释

```java
queueDeclare(
    String queue,       // 队列名称
    boolean durable,    // 是否持久化
    boolean exclusive,  // 是否排他（仅限当前连接）
    boolean autoDelete, // 是否自动删除
    Map<String, Object> arguments // 额外参数
);
```

| **参数** | **作用** | **常见值** |
|----------|----------|------------|
| `queue` | 队列名称 | `"myQueue"` / `""` |
| `durable` | 持久化 | `true` / `false` |
| `exclusive` | 仅当前连接访问 | `true` / `false` |
| `autoDelete` | 无消费者后删除 | `true` / `false` |
| `x-expires` | 队列 TTL | `30000`（30s） |
| `x-message-ttl` | 消息 TTL | `60000`（60s） |
| `x-dead-letter-exchange` | 死信交换机 | `"dlx_exchange"` |
| `x-max-length` | 队列最大长度 | `100` |


## 3.`basicPublish` 方法解释

```java
basicPublish(
    String exchange,    // 交换机名称
    String routingKey,  // 路由键
    AMQP.BasicProperties props, // 消息属性（可选）
    byte[] body         // 消息内容
);
```

---

### **参数详解**
| 参数 | 类型 | 说明 |
|------|------|------|
| `exchange` | `String` | **交换机名称**，`""` 表示默认（`default exchange`）。 |
| `routingKey` | `String` | **路由键**，决定消息发送到哪个队列。 |
| `props` | `AMQP.BasicProperties` | **消息属性**，可用于设置**持久化、TTL、优先级、消息 ID 等**，`null` 表示默认。 |
| `body` | `byte[]` | **消息体**，需要转换成 `byte[]` 发送。 |

## 4.源码

`Message.java`

```java
package com.hello.demo.rabbitmq.common;

import java.util.UUID;

public class Message {

    private String messageId;

    private String message;

    private String createAt;

    public Message() {
        this.messageId = UUID.randomUUID().toString();
        this.message = "Hello: " + UUID.randomUUID().toString();
        this.createAt = String.valueOf(System.currentTimeMillis());
    }

    public String getMessageId() {
        return messageId;
    }

    public void setMessageId(String messageId) {
        this.messageId = messageId;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public String getCreateAt() {
        return createAt;
    }

    public void setCreateAt(String createAt) {
        this.createAt = createAt;
    }

    @Override
    public String toString() {
        return "Message{" +
                "messageId='" + messageId + '\'' +
                ", message='" + message + '\'' +
                ", createAt='" + createAt + '\'' +
                '}';
    }
}


```

`ProducerDemo.java`

```java
package com.hello.demo.rabbitmq;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.hello.demo.rabbitmq.common.Message;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class ProducerDemo {

    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.43.242");
        factory.setPort(5672);
        factory.setUsername("hello");
        factory.setPassword("hello123456");

        Connection connection = factory.newConnection();
        // 虚拟信道，复用同一tcp连接
        Channel channel = connection.createChannel();
        // 创建交换机
        channel.exchangeDeclare("logs", "topic", true);
        // 创建队列
        channel.queueDeclare("logs_info", true,false, false, null);
        // 绑定(交换机与队列建立联系)
        channel.queueBind("logs_info", "logs", "log.info");

        Message message = new Message();
        ObjectMapper objectMapper = new ObjectMapper();
        String json = objectMapper.writeValueAsString(message);

        // 发布消息
        channel.basicPublish("logs", "log.info", null, json.getBytes());

        connection.close();
    }
}
```

`ConsumerDemo.java`

```java
package com.hello.demo.rabbitmq;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.hello.demo.rabbitmq.common.Message;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.DeliverCallback;

import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.util.concurrent.TimeoutException;

public class ConsumerDemo {

    public static void main(String[] args) throws IOException, TimeoutException {

        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.43.242");
        factory.setPort(5672);
        factory.setUsername("hello");
        factory.setPassword("hello123456");

        Connection connection = factory.newConnection();
        // 虚拟信道，复用同一tcp连接
        Channel channel = connection.createChannel();

        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            System.out.println("consumerTag: " + consumerTag);
            String data = new String(delivery.getBody(), StandardCharsets.UTF_8);
            ObjectMapper objectMapper = new ObjectMapper();
            Message message = objectMapper.readValue(data, Message.class);
            System.out.println("message: " + message.toString());
        };
        channel.basicConsume("logs_info", true, deliverCallback, consumerTag -> {});
        channel.close();
    }
}


```