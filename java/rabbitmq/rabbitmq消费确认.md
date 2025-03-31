# rabbitmq 消费确认

在 RabbitMQ 中，消费端可以选择**手动确认（Manual Acknowledgment）**或**自动确认（Auto Acknowledgment）**，用于保证消息被正确处理并防止消息丢失。

---

## 1.消费确认方式
| 确认模式 | 方式 | 适用场景 | 风险 |
|---------|------|------|------|
| **自动确认**（Auto Ack） | `autoAck = true` | 适用于非重要数据，如日志 | 可能丢失未处理完的消息 |
| **手动确认**（Manual Ack） | `autoAck = false` + `channel.basicAck()` | **推荐**，防止消息丢失 | 需要自己处理消息确认 |
| **拒绝消息**（Reject / Nack） | `basicReject()` / `basicNack()` | 允许重新放回队列 | 可能导致消息堆积 |

---

## 1. 自动确认模式（Auto Acknowledgment）**
当消费者**接收消息**后，RabbitMQ **立即确认**，无论消费者是否处理成功。


## 2. 手动确认模式（推荐）
如果 `autoAck = false`，则必须显式调用 `basicAck()` 确认，否则 RabbitMQ **不会删除消息**，消息会被**重新投递**。


## 3. 拒绝消息（Reject / Nack）

### 方式 1：`basicReject()`（不可批量拒绝）
```java
channel.basicReject(deliveryTag, requeue);
```
| 参数 | 作用 |
|------|------|
| `deliveryTag` | 当前消息的唯一标识 |
| `requeue = true` | 重新放回队列 |
| `requeue = false` | 直接丢弃 |

---

### 方式 2：`basicNack()`（支持批量拒绝）
```java
channel.basicNack(deliveryTag, multiple, requeue);
```
| 参数 | 作用 |
|------|------|
| `multiple = true` | 拒绝所有 `deliveryTag` 之前的消息 |
| `multiple = false` | 只拒绝当前消息 |
| `requeue = true` | 重新放回队列 |
| `requeue = false` | 丢弃（可能进入死信队列） |


## 4.源码示例

`ConsumerConfirm.java`

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

public class ConsumerConfirm {

    // 策略1: 自动确认
    public static void case1() throws IOException, TimeoutException {
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

    // 策略2: 手动确认
    public static void case2() throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.43.242");
        factory.setPort(5672);
        factory.setUsername("hello");
        factory.setPassword("hello123456");

        Connection connection = factory.newConnection();
        // 虚拟信道，复用同一tcp连接
        Channel channel = connection.createChannel();

        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            try {
                System.out.println("consumerTag: " + consumerTag);
                String data = new String(delivery.getBody(), StandardCharsets.UTF_8);
                ObjectMapper objectMapper = new ObjectMapper();
                Message message = objectMapper.readValue(data, Message.class);
                System.out.println("message: " + message.toString());
                channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
            } catch (Exception e) {
                // 未确认
                channel.basicReject(delivery.getEnvelope().getDeliveryTag(), true);
                // 支持批量未确认
                // channel.basicNack(delivery.getEnvelope().getDeliveryTag(), false, true);
            }

        };
        channel.basicConsume("logs_info", false, deliverCallback, consumerTag -> {});

//        channel.close();
    }

    public static void main(String[] args) {
        try {
            case2();
        }
        catch (Exception e) {
            System.out.println("error: " + e);
        }
    }
}

```

