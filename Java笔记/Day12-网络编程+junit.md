### 网络编程

#### 概念

- 常见软件架构：Client-Server（CS）客户端-服务端、Browser-Server（BS）浏览器-服务端
- 三要素：IP地址、端口号（0-65535）、协议

- UDP特点：不需要连接、速度快、一次最多发送64k，易丢失数据
- TCP特点：需要连接、速度慢、没有大小限制、不易丢失数据



#### UDP

- 注意是DatagramSocket和DatagramPacket两类
- 接收端是在`DatagramSocket(int port)`写端口号
- 发送端是在`DatagramPacket(byte buf[], int offset, int length,InetAddress address, int port)`写IP地址和端口号

```java
//服务端，注意先运行服务端
public class UDPReceiver {
    public static void main(String[] args) throws IOException {
        System.out.println("接收端启动!");
        // 1.创建接收端
        DatagramSocket socket = new DatagramSocket(6666);

        // 2.创建空的数据包
        byte[] buf = new byte[1024];

        // 3.接收数据, 接收到的数据放到包中
        DatagramPacket packet = new DatagramPacket(buf, buf.length);
        socket.receive(packet);

        //要获取接收后的长度
        int length = packet.getLength();
        System.out.println(new String(buf, 0, length));

        // 4.关闭资源
        socket.close();
    }
}
```

```java
//客户端
public class UDPSender {
    public static void main(String[] args) throws IOException {
        System.out.println("发送端启动!");
        // 1.创建发送端
        DatagramSocket socket = new DatagramSocket();

        // 2.创建数据包
        byte[] buf = "你好UDP!".getBytes();
        DatagramPacket packet = new DatagramPacket(buf, 0, buf.length, InetAddress.getByName("127.0.0.1"), 6666);

        // 3.发送数据
        socket.send(packet);

        // 4.关闭资源
        socket.close();
    }
}
```



#### TCP

- ServerSocket只需要在服务端创建
- 客户端需要在创建Socket对象的参数中明确服务端的IP地址和端口号

```java
//服务端
public class TCPServer {
    public static void main(String[] args) throws IOException {
        System.out.println("服务端启动啦!");
        // 1.创建TCP服务端
        ServerSocket serverSocket = new ServerSocket(10086);

        // 3.同意客户端的请求, 如果没有客户端连接就一直等
        Socket socket = serverSocket.accept();

        // 5.得到输入流读取数据
        InputStream in = socket.getInputStream();
        byte[] buf = new byte[1024];
        int len = in.read(buf);
        System.out.println("服务端接收到:" + new String(buf, 0, len));

        // 6.输出流发送数据
        OutputStream out = socket.getOutputStream();
        out.write("好呀！老地方见！".getBytes());

        // 关闭资源
        out.close();
        in.close();
        socket.close();
        serverSocket.close();
    }
}
```

```java
//客户端
public class TCPClient {
    public static void main(String[] args) throws IOException {
        System.out.println("客户端启动啦!");
        // 2.创建客户端
        Socket socket = new Socket("127.0.0.1", 10086);

        // 4.输出流发送数据
        OutputStream out = socket.getOutputStream();
        out.write("你好约吗?".getBytes());

        // 7.输入流读取数据
        InputStream in = socket.getInputStream();
        byte[] buf = new byte[1024];
        int len = in.read(buf);
        System.out.println("客户端收到:" + new String(buf, 0, len));

        // 关闭资源 从下往上关闭流
        in.close();
        out.close();
        socket.close();
    }
}
```



#### 文件上传

```java
//服务端
public class UploadServer {
    public static void main(String[] args) throws IOException {
        System.out.println("文件上传服务端启动啦!");
        // 1.创建服务端
        ServerSocket serverSocket = new ServerSocket(9999);

        // 2.同意客户端的连接
        Socket socket = serverSocket.accept();
        System.out.println("开始上传！");

        // 3.得到Socket输入流
        InputStream in = socket.getInputStream();

        // 4.创建文件输出流
        FileOutputStream fos = new FileOutputStream("study_day12\\upload\\1.png");

        // 5.循环读写数据
        byte[] buf = new byte[1024 * 8];
        int len;
        while ((len = in.read(buf)) != -1) {
            fos.write(buf, 0, len);
        }
        System.out.println("服务端接收完成!");

        // 6.得到Socket的输出流写数据
        OutputStream out = socket.getOutputStream();
        out.write("上传完成！".getBytes());

        // 7.关闭
        out.close();
        fos.close();
        in.close();
        socket.close();
        serverSocket.close();
    }
}
```

```java
//客户端
public class UploadClient {
    public static void main(String[] args) throws IOException {
        System.out.println("文件上传客户端启动啦!");
        // 1.创建客户端
        Socket socket = new Socket("127.0.0.1", 9999);

        // 2.创建文件输入流
        FileInputStream fis = new FileInputStream("study_day12\\abc\\xyz.png");

        // 3.得到Socket的输出流
        OutputStream out = socket.getOutputStream();

        // 4.循环读取数据
        byte[] buf = new byte[1024 * 8];
        int len;
        while ((len = fis.read(buf)) != -1) {
        // 输出流发送数据
            out.write(buf, 0, len);
        }
        System.out.println("客户端发送文件完毕！");
        socket.shutdownOutput(); //把客户端的流关掉，服务端就会停止获取输入

        // 5.得到Socket输入流读取数据
        InputStream in = socket.getInputStream();
        //buf 重复利用
        len = in.read(buf);
        System.out.println("客户端收到:" + new String(buf, 0, len));

        // 6.关闭资源
        in.close();
        out.close();
        fis.close();
        socket.close();

    }
}
```



#### 文件上传-多线程

```java
//服务端
public class UploadServer {
    public static void main(String[] args) throws IOException {
        System.out.println("文件上传服务端启动啦!");
        // 1.创建服务端
        ServerSocket serverSocket = new ServerSocket(9999);

        //多人同时上传
        ExecutorService pool = Executors.newFixedThreadPool(10);
        while (true) {
            // 2.同意客户端的连接
            //不能放进线程任务中，否则就会在没有获取连接的情况下开线程，并且相当于开了10个服务，而不是10个线程
            Socket socket = serverSocket.accept();
            UploadRunnable up = new UploadRunnable(socket);
        }
        //serverSocket.close();持续等待就不能关闭服务端
    }
}
```

```java
//创建线程
public class UploadRunnable implements Runnable {
    //成员变量
    private Socket socket;

    //构造方法给成员变量赋值
    public UploadRunnable(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            System.out.println("开始上传！");
            // 3.得到Socket输入流
            InputStream in = socket.getInputStream();

            // 4.创建文件输出流
            //给文件命名，JDK自带的，能够生成一个随机的字符串
            String str = UUID.randomUUID().toString();
            FileOutputStream fos = new FileOutputStream("study_day12\\upload\\" + str + ".png");

            // 5.循环读写数据
            byte[] buf = new byte[1024 * 8];
            int len;
            while ((len = in.read(buf)) != -1) {
                fos.write(buf, 0, len);
            }
            System.out.println("服务端接收完成!");

            // 6.得到Socket的输出流写数据
            OutputStream out = socket.getOutputStream();
            out.write("上传完成！".getBytes());

            // 7.关闭
            out.close();
            fos.close();
            in.close();
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

```java
//客户端
public class UploadClient {
    public static void main(String[] args) throws IOException {
        System.out.println("文件上传客户端启动啦!");
        int n = 0;
        //模拟有20个用户来访问
        while (n <= 20) {
            n++;
            new Thread(() -> {
                try {
                    // 1.创建客户端
                    Socket socket = new Socket("127.0.0.1", 9999);
                    // 2.创建文件输入流
                    FileInputStream fis = new FileInputStream("study_day12\\abc\\xyz.png");
                    // 3.得到Socket的输出流
                    OutputStream out = socket.getOutputStream();
                    // 4.循环读写数据
                    byte[] buf = new byte[1024 * 8];
                    int len;
                    while ((len = fis.read(buf)) != -1) {
                        out.write(buf, 0, len);
                    }
                    System.out.println("客户端发送文件完毕！");
                    socket.shutdownOutput(); //把客户端的流关掉，服务端就会停止获取输入
                    // 5.得到Socket输入流读取数据
                    InputStream in = socket.getInputStream();
                    //buf 重复利用
                    len = in.read(buf);
                    System.out.println("客户端收到:" + new String(buf, 0, len));
                    // 6.关闭资源
                    in.close();
                    out.close();
                    fis.close();
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            ).start();
        }
    }
}
```



#### 模拟网站服务器

```java
public class WebServer {
    public static void main(String[] args) throws IOException {
        System.out.println("服务器启动啦!");
        // 1.创建TCP服务端
        ServerSocket serverSocket = new ServerSocket(9527);
        // 2.同意客户端的请求
        while (true) {
            // 2.同意客户端的请求
            Socket socket = serverSocket.accept();
            // 3.返回数据给浏览器(TCP客户端)HTTP协议在返回数据时有固定的格式
            OutputStream output = socket.getOutputStream();
            output.write("HTTP/1.1 200 OK\r\n".getBytes()); // 告诉浏览器一切正常
            output.write("Content-Type:text/html;charset=utf-8\r\n".getBytes()); // 告诉浏览器返回的是一个html网页,是utf-8编码
            output.write("\r\n".getBytes());
            output.write("<img src=\"https://tm9uzq.github.io/book/logo.png\">".getBytes());
        }
    }
}
```



#### 单元测试

- Junit是一个Java中常用的第三方单元测试工具，可对Java中的方法进行测试，提高代码测试效率

- 使用流程

  - 编写测试方法
  - 在测试方法上使用`@Test`
  - 运行单元测试方法

- 使用要求

  - 方法必须是public
  - 返回值必须是void
  - 不能有参数

- 常用注解

  - Junit 4.xx版本

    | 注解名       | 说明                                           |
    | ------------ | ---------------------------------------------- |
    | @Test        | 单元测试方法                                   |
    | @Before      | 在每个测试的方法前运行                         |
    | @After       | 在每个测试的方法后运行                         |
    | @BeforeClass | 在所有测试的方法前运行一次【方法必须是静态的】 |
    | @AfterClass  | 在所有测试的方法后运行一次【方法必须是静态的】 |

  - Junit 5.xx版本

    | 注解名      | 说明                       |
    | ----------- | -------------------------- |
    | @Test       | 单元测试方法               |
    | @BeforeEach | 在每个测试的方法前运行     |
    | @AfterEach  | 在每个测试的方法后运行     |
    | @BeforeAll  | 在所有测试的方法前运行一次 |
    | @AfterAll   | 在所有测试的方法后运行一次 |



#### commons-io

- commons-io是apache提供的一组有关IO操作的类库，有两个主要的类FileUtils, IOUtils

- FileUtils主要方法

  | 方法名                                                      | 说明                        |
  | ----------------------------------------------------------- | --------------------------- |
  | String **readFileToString**(Filefile, String encoding)      | 读取文件中的数据,返回字符串 |
  | void **copyFile**(FilesrcFile, File destFile)               | 复制文件                    |
  | void **copyDirectoryToDirectory**(FilesrcDir, File destDir) | 复制文件夹                  |



#### NIO

- JDK1.4以前: InputStream/OutputStream称为BIO(Blocking IO)    阻塞式IO
  - 阻塞：如果没有数据就一直等待
- JDK1.4推出了一套新的IO体系称为NIO (New IO/ Not Blocking IO)   非阻塞式IO 
  - 非阻塞：如果没有数据，不会一直等待，可以做其他事
- NIO的三个角色
  - Channel通道
    - 可以双向传输数据
  - ByteBuffer
    - 相当于BIO中的byte[]，效率更高，功能更强大，可以保存要发送和接收的数据
  - Selector选择器
    - 使用了多路复用，可以管理多个连接