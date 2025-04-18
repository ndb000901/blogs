# UDP Echo 示例


## 1. UDP Echo Server 示例

```java
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketException;

public class UdpEchoServer {

    private static final int PORT = 12345;
    private static final int BUFFER_SIZE = 1024;

    public static void main(String[] args) {
        System.out.println("UDP Echo 服务器启动，监听端口 " + PORT);
        try (DatagramSocket socket = new DatagramSocket(PORT)) {
            byte[] buffer = new byte[BUFFER_SIZE];

            while (true) {
                // 接收数据包
                DatagramPacket request = new DatagramPacket(buffer, buffer.length);
                socket.receive(request); // 阻塞等待客户端发送数据

                String received = new String(request.getData(), 0, request.getLength());
                System.out.println("收到数据: " + received +
                        "，来自: " + request.getAddress() + ":" + request.getPort());

                // 构造响应数据包
                DatagramPacket response = new DatagramPacket(
                        request.getData(),
                        request.getLength(),
                        request.getAddress(),
                        request.getPort());

                // 发送数据
                socket.send(response);
            }

        } catch (SocketException e) {
            System.err.println("Socket 创建失败: " + e.getMessage());
        } catch (IOException e) {
            System.err.println("IO 异常: " + e.getMessage());
        }
    }
}
```

---

## 2. UDP 客户端简单示例

```java
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;

public class UdpEchoClient {

    public static void main(String[] args) throws Exception {
        DatagramSocket socket = new DatagramSocket();
        InetAddress serverAddress = InetAddress.getByName("localhost");
        int serverPort = 12345;

        String message = "Hello UDP Server";
        byte[] buffer = message.getBytes();

        // 发送
        DatagramPacket packet = new DatagramPacket(buffer, buffer.length, serverAddress, serverPort);
        socket.send(packet);

        // 接收响应
        byte[] responseBuffer = new byte[1024];
        DatagramPacket response = new DatagramPacket(responseBuffer, responseBuffer.length);
        socket.receive(response);

        String echoed = new String(response.getData(), 0, response.getLength());
        System.out.println("收到服务器响应: " + echoed);

        socket.close();
    }
}
```

---


