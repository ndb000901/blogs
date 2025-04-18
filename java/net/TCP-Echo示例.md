# Tcp Echo示例

## 1. Echo Server 代码（多线程版本）

```java
import java.io.*;
import java.net.*;

public class EchoServer {

    private static final int PORT = 12345;

    public static void main(String[] args) {
        System.out.println("Echo Server 启动，监听端口 " + PORT);

        try (ServerSocket serverSocket = new ServerSocket(PORT)) {
            while (true) {
                // 接受客户端连接
                Socket clientSocket = serverSocket.accept();
                System.out.println("客户端连接: " + clientSocket.getRemoteSocketAddress());

                // 启动新线程处理该客户端
                new Thread(() -> handleClient(clientSocket)).start();
            }
        } catch (IOException e) {
            System.err.println("服务器异常: " + e.getMessage());
            e.printStackTrace();
        }
    }

    private static void handleClient(Socket clientSocket) {
        try (
            BufferedReader in = new BufferedReader(
                    new InputStreamReader(clientSocket.getInputStream()));
            BufferedWriter out = new BufferedWriter(
                    new OutputStreamWriter(clientSocket.getOutputStream()))
        ) {
            String line;
            while ((line = in.readLine()) != null) {
                System.out.println("收到客户端消息: " + line);
                out.write("Echo: " + line + "\n");
                out.flush();
            }
        } catch (IOException e) {
            System.err.println("客户端通信异常: " + e.getMessage());
        } finally {
            try {
                System.out.println("关闭客户端连接: " + clientSocket.getRemoteSocketAddress());
                clientSocket.close();
            } catch (IOException e) {
                System.err.println("关闭 socket 出错: " + e.getMessage());
            }
        }
    }
}
```

---



## 2. TCP Echo 客户端（标准阻塞 IO）

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.PrintWriter;
import java.net.Socket;

public class TcpEchoClient {

    public static void main(String[] args) {
        String serverHost = "localhost"; // 或使用实际 IP
        int serverPort = 12345;

        try (Socket socket = new Socket(serverHost, serverPort)) {
            System.out.println("已连接到服务器: " + serverHost + ":" + serverPort);

            // 获取输入输出流
            BufferedReader reader = new BufferedReader(
                    new InputStreamReader(socket.getInputStream())
            );
            PrintWriter writer = new PrintWriter(socket.getOutputStream(), true);

            // 发送一条消息
            String message = "Hello TCP Server";
            writer.println(message);
            System.out.println("发送消息: " + message);

            // 接收服务器响应
            String response = reader.readLine();
            System.out.println("收到服务器响应: " + response);

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---


