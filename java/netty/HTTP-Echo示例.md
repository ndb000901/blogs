# HTTP Echo 示例

## 1. `HttpEchoServer.java`

```java
import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.http.*;

public class HttpEchoServer {

    private final int port;

    public HttpEchoServer(int port) {
        this.port = port;
    }

    public void start() throws InterruptedException {
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
             .channel(NioServerSocketChannel.class)
             .childHandler(new ChannelInitializer<SocketChannel>() {
                 @Override
                 protected void initChannel(SocketChannel ch) {
                     ch.pipeline().addLast(new HttpServerCodec());
                     ch.pipeline().addLast(new HttpObjectAggregator(65536)); // 聚合成 FullHttpRequest
                     ch.pipeline().addLast(new HttpEchoServerHandler());
                 }
             });

            ChannelFuture f = b.bind(port).sync();
            System.out.println("HTTP Echo Server started on port " + port);
            f.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new HttpEchoServer(8080).start();
    }
}
```

---

## 2. `HttpEchoServerHandler.java`

```java
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.handler.codec.http.*;
import io.netty.util.CharsetUtil;

public class HttpEchoServerHandler extends SimpleChannelInboundHandler<FullHttpRequest> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, FullHttpRequest request) {
        System.out.println("Received HTTP request: " + request.uri());

        StringBuilder responseContent = new StringBuilder();
        responseContent.append("Method: ").append(request.method()).append("\n");
        responseContent.append("URI: ").append(request.uri()).append("\n\n");

        responseContent.append("Headers:\n");
        for (String name : request.headers().names()) {
            responseContent.append(name).append(": ").append(request.headers().get(name)).append("\n");
        }

        responseContent.append("\nBody:\n");
        responseContent.append(request.content().toString(CharsetUtil.UTF_8));

        FullHttpResponse response = new DefaultFullHttpResponse(
                HttpVersion.HTTP_1_1,
                HttpResponseStatus.OK,
                Unpooled.copiedBuffer(responseContent.toString(), CharsetUtil.UTF_8)
        );

        response.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/plain; charset=UTF-8");
        response.headers().set(HttpHeaderNames.CONTENT_LENGTH, response.content().readableBytes());

        ctx.writeAndFlush(response);
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        cause.printStackTrace();
        ctx.close();
    }
}
```

---


## 3. `HttpEchoClient.java`

```java
import io.netty.bootstrap.Bootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.http.*;
import io.netty.buffer.Unpooled;
import io.netty.util.CharsetUtil;

import java.net.URI;

public class HttpEchoClient {

    public static void main(String[] args) throws Exception {
        URI uri = new URI("http://localhost:8080/echo");
        String host = uri.getHost();

        EventLoopGroup group = new NioEventLoopGroup();

        try {
            Bootstrap b = new Bootstrap();
            b.group(group)
             .channel(NioSocketChannel.class)
             .handler(new ChannelInitializer<Channel>() {
                 @Override
                 protected void initChannel(Channel ch) {
                     ch.pipeline().addLast(new HttpClientCodec());
                     ch.pipeline().addLast(new HttpObjectAggregator(8192));
                     ch.pipeline().addLast(new HttpEchoClientHandler());
                 }
             });

            Channel channel = b.connect(host, 8080).sync().channel();

            // 准备请求内容
            String content = "Hello, this is Netty HTTP Echo Client!";
            FullHttpRequest request = new DefaultFullHttpRequest(
                    HttpVersion.HTTP_1_1,
                    HttpMethod.POST,
                    uri.getRawPath(),
                    Unpooled.copiedBuffer(content, CharsetUtil.UTF_8)
            );

            request.headers().set(HttpHeaderNames.HOST, host);
            request.headers().set(HttpHeaderNames.CONNECTION, HttpHeaderValues.CLOSE);
            request.headers().set(HttpHeaderNames.CONTENT_LENGTH, request.content().readableBytes());
            request.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/plain; charset=UTF-8");

            channel.writeAndFlush(request);
            channel.closeFuture().sync();

        } finally {
            group.shutdownGracefully();
        }
    }
}
```

---

## 4. `HttpEchoClientHandler.java`

```java
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.handler.codec.http.*;

import io.netty.util.CharsetUtil;

public class HttpEchoClientHandler extends SimpleChannelInboundHandler<FullHttpResponse> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, FullHttpResponse response) {
        System.out.println("RESPONSE STATUS: " + response.status());
        System.out.println("RESPONSE HEADERS: " + response.headers());
        System.out.println("RESPONSE BODY:\n" + response.content().toString(CharsetUtil.UTF_8));
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        cause.printStackTrace();
        ctx.close();
    }
}
```


