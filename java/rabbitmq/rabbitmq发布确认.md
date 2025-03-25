# rabbitmq发布确认



* **简单发布确认：** 生产者发送一条消息后，等待 RabbitMQ 的确认。这种方式简单，但性能较低，因为它需要为每条消息等待确认。
* **批量发布确认：** 生产者发送一批消息后，等待 RabbitMQ 的确认。这种方式提高了性能，因为它减少了确认的次数。
* **异步发布确认：** 生产者发送消息后，通过回调函数异步处理 RabbitMQ 的确认。这种方式提供了更高的性能和灵活性。


## 源码示例

`PublishingConfirmDemo.java`

```java
package com.hello.demo.rabbitmq;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.hello.demo.rabbitmq.common.Message;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.SortedSet;
import java.util.concurrent.ConcurrentSkipListSet;
import java.util.concurrent.TimeoutException;

public class PublishingConfirmDemo {

    // 策略1: 确认后再发布下一个
    public static void case1() throws IOException, TimeoutException, InterruptedException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.43.242");
        factory.setPort(5672);
        factory.setUsername("hello");
        factory.setPassword("hello123456");

        Connection connection = factory.newConnection();
        // 虚拟信道，复用同一tcp连接
        Channel channel = connection.createChannel();
        // 启用发布确认模式
        channel.confirmSelect();
        // 创建交换机
        channel.exchangeDeclare("logs", "topic", true);
        // 创建队列
        channel.queueDeclare("logs_info", true,false, false, null);
        // 绑定(交换机与队列建立联系)
        channel.queueBind("logs_info", "logs", "log.info");

        Message message = new Message();
        ObjectMapper objectMapper = new ObjectMapper();
        String json = objectMapper.writeValueAsString(message);

        for (int i = 0; i < 10_0000; i++) {
            // 发布消息
            channel.basicPublish("logs", "log.info", null, json.getBytes());
            // 等待确认
            channel.waitForConfirmsOrDie(5000);
        }

        connection.close();
    }

    // 策略2: 批量发送，等待确认
    public static void case2() throws IOException, TimeoutException, InterruptedException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.43.242");
        factory.setPort(5672);
        factory.setUsername("hello");
        factory.setPassword("hello123456");

        Connection connection = factory.newConnection();
        // 虚拟信道，复用同一tcp连接
        Channel channel = connection.createChannel();
        // 启用发布确认模式
        channel.confirmSelect();
        // 创建交换机
        channel.exchangeDeclare("logs", "topic", true);
        // 创建队列
        channel.queueDeclare("logs_info", true,false, false, null);
        // 绑定(交换机与队列建立联系)
        channel.queueBind("logs_info", "logs", "log.info");

        Message message = new Message();
        ObjectMapper objectMapper = new ObjectMapper();
        String json = objectMapper.writeValueAsString(message);

        int count = 0;
        for (int i = 0; i < 10_0000; i++) {
            count++;
            // 发布消息
            channel.basicPublish("logs", "log.info", null, json.getBytes());
            if (count == 100) {
                // 等待确认
                channel.waitForConfirmsOrDie(5000);
                count = 0;
            }
        }

        connection.close();
    }

    // 策略3: 异步确认
    public static void case3() throws IOException, TimeoutException, InterruptedException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.43.242");
        factory.setPort(5672);
        factory.setUsername("hello");
        factory.setPassword("hello123456");

        Connection connection = factory.newConnection();
        // 虚拟信道，复用同一tcp连接
        Channel channel = connection.createChannel();
        // 启用发布确认模式
        channel.confirmSelect();
        // 创建交换机
        channel.exchangeDeclare("logs", "topic", true);
        // 创建队列
        channel.queueDeclare("logs_info", true,false, false, null);
        // 绑定(交换机与队列建立联系)
        channel.queueBind("logs_info", "logs", "log.info");

        // 记录未确认的消息
        SortedSet<Long> unconfirmedSet = new ConcurrentSkipListSet<>();

        // 监听消息确认（成功确认回调
        // 回调1: 返回已确认消息， 回调2: 返回未确认消息
        channel.addConfirmListener((deliveryTag, multiple) -> {
            System.out.println("deliveryTag: " + deliveryTag);
            if (multiple) {
                unconfirmedSet.headSet(deliveryTag + 1).clear();
            } else {
                unconfirmedSet.remove(deliveryTag);
            }
            System.out.println("Message confirmed: " + deliveryTag);
        }, (deliveryTag, multiple) -> {
            System.err.println("Message lost: " + deliveryTag);
        });

        Message message = new Message();
        ObjectMapper objectMapper = new ObjectMapper();
        String json = objectMapper.writeValueAsString(message);

        for (int i = 0; i < 1; i++) {
            long nextId = channel.getNextPublishSeqNo();
            unconfirmedSet.add(nextId);
            // 发布消息
            channel.basicPublish("logs", "log.info", null, json.getBytes());
            System.out.println("nextId: " + nextId);
        }

        connection.close();
    }


    public static void main(String[] args) {
        try {
            case3();
        }
        catch (Exception e) {
            System.out.println("error" + e);
        }

    }
}

```
