# UDP echo 示例

## 1. 依赖
建议使用 Maven，添加 Netty 依赖：
```xml
<!-- https://mvnrepository.com/artifact/io.netty/netty-all -->
<dependency>
    <groupId>io.netty</groupId>
    <artifactId>netty-all</artifactId>
    <version>4.2.0.Final</version>
</dependency>
```

---

## 2. UdpEchoServer.java
```java
import io.netty.bootstrap.Bootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioDatagramChannel;

public class UdpEchoServer {

    private final int port;

    public UdpEchoServer(int port) {
        this.port = port;
    }

    public void run() throws InterruptedException {
        EventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap b = new Bootstrap();
            b.group(group)
             .channel(NioDatagramChannel.class)
             .option(ChannelOption.SO_BROADCAST, true)
             .handler(new ChannelInitializer<NioDatagramChannel>() {
                 @Override
                 protected void initChannel(NioDatagramChannel ch) {
                     ChannelPipeline p = ch.pipeline();
                     p.addLast(new UdpEchoServerHandler());
                 }
             });

            Channel ch = b.bind(port).sync().channel();
            System.out.println("UDP Echo Server started on port " + port);
            ch.closeFuture().await();
        } finally {
            group.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new UdpEchoServer(8080).run();
    }
}
```

---

## 3. UdpEchoServerHandler.java
```java
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.channel.socket.DatagramPacket;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.util.CharsetUtil;
import java.net.InetSocketAddress;

public class UdpEchoServerHandler extends SimpleChannelInboundHandler<DatagramPacket> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, DatagramPacket packet) {
        ByteBuf content = packet.content();
        String msg = content.toString(CharsetUtil.UTF_8);
        System.out.println("Server received: " + msg);

        // Echo back
        ByteBuf response = Unpooled.copiedBuffer("Echo: " + msg, CharsetUtil.UTF_8);
        DatagramPacket echo = new DatagramPacket(response, packet.sender());
        ctx.writeAndFlush(echo);
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        cause.printStackTrace();
    }
}
```

---

## 4. UdpEchoClient.java
```java
import io.netty.bootstrap.Bootstrap;
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioDatagramChannel;
import io.netty.channel.socket.DatagramPacket;
import io.netty.util.CharsetUtil;

import java.net.InetSocketAddress;

public class UdpEchoClient {

    private final String host;
    private final int port;

    public UdpEchoClient(String host, int port) {
        this.host = host;
        this.port = port;
    }

    public void run(String message) throws InterruptedException {
        EventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap b = new Bootstrap();
            b.group(group)
             .channel(NioDatagramChannel.class)
             .option(ChannelOption.SO_BROADCAST, true)
             .handler(new ChannelInitializer<NioDatagramChannel>() {
                 @Override
                 protected void initChannel(NioDatagramChannel ch) {
                     ch.pipeline().addLast(new UdpEchoClientHandler());
                 }
             });

            Channel ch = b.bind(0).sync().channel();

            // 发送消息
            DatagramPacket packet = new DatagramPacket(
                    Unpooled.copiedBuffer(message, CharsetUtil.UTF_8),
                    new InetSocketAddress(host, port));
            ch.writeAndFlush(packet).sync();

            // 等待响应（带超时）
            if (!ch.closeFuture().await(5000)) {
                System.out.println("Client timed out.");
            }
        } finally {
            group.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new UdpEchoClient("127.0.0.1", 8080).run("Hello Netty UDP!");
    }
}
```

---

## 5. UdpEchoClientHandler.java
```java
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.channel.socket.DatagramPacket;
import io.netty.util.CharsetUtil;

public class UdpEchoClientHandler extends SimpleChannelInboundHandler<DatagramPacket> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, DatagramPacket msg) {
        String response = msg.content().toString(CharsetUtil.UTF_8);
        System.out.println("Client received: " + response);
        ctx.close(); // 关闭通道
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        cause.printStackTrace();
        ctx.close();
    }
}
```

---

