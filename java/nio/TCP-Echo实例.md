# Tcp Echo示例


## 1. NIO 实现的 TCP Echo 服务器（支持多个客户端）

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;

public class NioTcpEchoServer {
    public static void main(String[] args) throws IOException {
        int port = 12345;
        Selector selector = Selector.open();

        ServerSocketChannel serverChannel = ServerSocketChannel.open();
        serverChannel.bind(new InetSocketAddress(port));
        serverChannel.configureBlocking(false);
        serverChannel.register(selector, SelectionKey.OP_ACCEPT);

        System.out.println("NIO TCP Echo Server 启动，监听端口：" + port);

        ByteBuffer buffer = ByteBuffer.allocate(1024);

        while (true) {
            selector.select(); // 阻塞直到有事件
            Iterator<SelectionKey> it = selector.selectedKeys().iterator();

            while (it.hasNext()) {
                SelectionKey key = it.next();
                it.remove();

                if (key.isAcceptable()) {
                    SocketChannel clientChannel = serverChannel.accept();
                    clientChannel.configureBlocking(false);
                    clientChannel.register(selector, SelectionKey.OP_READ);
                    System.out.println("新连接: " + clientChannel.getRemoteAddress());

                } else if (key.isReadable()) {
                    SocketChannel clientChannel = (SocketChannel) key.channel();
                    buffer.clear();
                    int read = clientChannel.read(buffer);

                    if (read == -1) {
                        System.out.println("连接关闭: " + clientChannel.getRemoteAddress());
                        clientChannel.close();
                        key.cancel();
                        continue;
                    }

                    buffer.flip();
                    String received = new String(buffer.array(), 0, buffer.limit());
                    System.out.println("收到消息: " + received.trim());

                    // Echo 回客户端
                    clientChannel.write(buffer);
                }
            }
        }
    }
}
```

---

## 2. NIO TCP 客户端示例

```java
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;

public class NioTcpEchoClient {
    public static void main(String[] args) throws Exception {
        SocketChannel channel = SocketChannel.open();
        channel.connect(new InetSocketAddress("localhost", 12345));
        channel.configureBlocking(true); // 简化客户端代码，使用阻塞方式

        String message = "Hello from NIO TCP Client!";
        ByteBuffer buffer = ByteBuffer.wrap(message.getBytes());
        channel.write(buffer);
        System.out.println("发送消息: " + message);

        buffer.clear();
        int bytesRead = channel.read(buffer);
        buffer.flip();

        byte[] data = new byte[buffer.limit()];
        buffer.get(data);

        System.out.println("收到服务器响应: " + new String(data));

        channel.close();
    }
}
```

---



