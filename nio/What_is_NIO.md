# What is NIO



NIO 需要从两个层面来解释，系统层面是讲的是 no block io 及 非阻塞io, Java 层面上讲是 new io . 与之想对应的是传统的IO ,BIO 阻塞性io





## BIO

阻塞性IO，特点是：每连接每线程 , 弊端：线程太多，调度 资源 ,阻塞

BIO 示例代码

```java
public class ServerSocketDemo {

    public static void main(String[] args) throws IOException {
            // 创建服务端socket
            int port = 8088;
            ServerSocket serverSocket = new ServerSocket(port);

            System.out.println("server start .... in port:"+port);

            //循环监听等待客户端的连接
            while (true) {
                // 监听客户端
                // 创建客户端socket
                Socket client  = serverSocket.accept();//阻塞1
                int clientPort =  client.getPort();
                System.out.println("client,port:"+clientPort);
                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        InputStream in = null;
                        try{
                            in = client.getInputStream();
                            BufferedReader reader = new BufferedReader(new InputStreamReader(in));

                            while (true){
                                String dataLine = reader.readLine();//阻塞2
                                if(null != dataLine){
                                    System.out.println(dataLine);
                                }else {
                                    client.close();
                                    break;
                                }
                            }
                        }catch (Exception e){

                        }
                        System.out.println("client finished clientPort:"+clientPort);
                    }
                }).start();


            }
    }
}



```



## NIO
nio : NONBLOCK  一个线程处理多个连接

通过 系统调用 设置 NONBLOCK 使的 方法不停止，要么返回值，要么返回-1

#### 相关概念

 Nio --> 多路复用器 select poll epoll --> netty

NIO 示例代码：循环发起系统调用，消耗性能(通过多路复用器解决)

```java

public class NIOSocketDemo {


    public static void main(String[] args) throws IOException {
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        int port = 9999;
        serverSocketChannel.socket().bind(new InetSocketAddress(port));
        System.out.println("server start .... in port:"+port);
        //设置成非阻塞模式
        serverSocketChannel.configureBlocking(false);

        /**
         *
         * 循环发起系统调用，消耗性能
         */
        while(true){
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            SocketChannel socketChannel = serverSocketChannel.accept();
            System.out.println("socketChannel accept");
            //在非阻塞模式下，accept() 方法会立刻返回，如果还没有新进来的连接,返回的将是null。 因此，需要检查返回的SocketChannel是否是null.如：
            if(null != socketChannel){
                //do something with socketChannel...
                ByteBuffer byteBuffer =  ByteBuffer.allocate(20);
                //放开读取阻塞
                socketChannel.configureBlocking(false);
                while (true){
                    try {
                        TimeUnit.SECONDS.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    int read = socketChannel.read(byteBuffer);
                    System.out.println("read....."+read);
                    if(read>0){
                        byteBuffer.flip();  //make buffer ready for read
                        while(byteBuffer.hasRemaining()) {
                            System.out.println((char)byteBuffer.get());
                        }
                    }
                }

            }
        }

    }
}
```





#### 常用调试命令

```shell
#查看系统调用： 
strace -ff -o out java xxx
#连接端口
nc localhost 8088
```



