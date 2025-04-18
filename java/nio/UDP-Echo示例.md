# UDP Echo 实例


## 1. UDP Echo Server（NIO 非阻塞）

```java
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.DatagramChannel;
import java.nio.channels.Selector;
import java.nio.channels.SelectionKey;
import java.util.Iterator;

public class UdpEchoServerNio {
    public static void main(String[] args) throws Exception {
        int port = 12345;
        Selector selector = Selector.open();
        DatagramChannel channel = DatagramChannel.open();
        channel.bind(new InetSocketAddress(port));
        channel.configureBlocking(false);
        channel.register(selector, SelectionKey.OP_READ);

        System.out.println("UDP Echo NIO 服务启动，监听端口: " + port);

        ByteBuffer buffer = ByteBuffer.allocate(1024);

        while (true) {
            selector.select(); // 阻塞直到有事件
            Iterator<SelectionKey> it = selector.selectedKeys().iterator();
            while (it.hasNext()) {
                SelectionKey key = it.next();
                it.remove();

                if (key.isReadable()) {
                    DatagramChannel dc = (DatagramChannel) key.channel();
                    buffer.clear();
                    InetSocketAddress remoteAddr = (InetSocketAddress) dc.receive(buffer);
                    buffer.flip();

                    String received = new String(buffer.array(), 0, buffer.limit());
                    System.out.println("收到消息: " + received + " 来自: " + remoteAddr);

                    // Echo 回去
                    dc.send(buffer, remoteAddr);
                }
            }
        }
    }
}
```

---

## 2. UDP Echo Client（NIO）

```java
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.DatagramChannel;

public class UdpEchoClientNio {
    public static void main(String[] args) throws Exception {
        String host = "localhost";
        int port = 12345;

        DatagramChannel channel = DatagramChannel.open();
        channel.configureBlocking(false);

        InetSocketAddress serverAddr = new InetSocketAddress(host, port);

        String message = "Hello from NIO UDP Client";
        ByteBuffer sendBuf = ByteBuffer.wrap(message.getBytes());
        channel.send(sendBuf, serverAddr);
        System.out.println("发送消息: " + message);

        ByteBuffer recvBuf = ByteBuffer.allocate(1024);

        long start = System.currentTimeMillis();
        while (System.currentTimeMillis() - start < 3000) { // 等待3秒响应
            recvBuf.clear();
            InetSocketAddress responseAddr = (InetSocketAddress) channel.receive(recvBuf);
            if (responseAddr != null) {
                recvBuf.flip();
                byte[] data = new byte[recvBuf.limit()];
                recvBuf.get(data);
                System.out.println("收到服务器响应: " + new String(data));
                break;
            }
        }

        channel.close();
    }
}
```

---

