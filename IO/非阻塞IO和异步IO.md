## 非阻塞IO和异步IO

#### 阻塞模式 IO

```java
public class Server {

  public static void main(String[] args) throws IOException {

    ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();

    // 监听 8080 端口进来的 TCP 链接
    serverSocketChannel.socket().bind(new InetSocketAddress(8080));

    while (true) {

      // 这里会阻塞，直到有一个请求的连接进来
      SocketChannel socketChannel = serverSocketChannel.accept();

      // 开启一个新的线程来处理这个请求，然后在 while 循环中继续监听 8080 端口
      SocketHandler handler = new SocketHandler(socketChannel);
      new Thread(handler).start();
    }
  }
}
```

```java
public class SocketHandler implements Runnable {

  private SocketChannel socketChannel;

  public SocketHandler(SocketChannel socketChannel) {
    this.socketChannel = socketChannel;
  }

  @Override
  public void run() {

    ByteBuffer buffer = ByteBuffer.allocate(1024);
    try {
      // 将请求数据读入 Buffer 中
      int num;
      while ((num = socketChannel.read(buffer)) > 0) {
        // 读取 Buffer 内容之前先 flip 一下
        buffer.flip();

        // 提取 Buffer 中的数据
        byte[] bytes = new byte[num];
        buffer.get(bytes);

        String re = new String(bytes, "UTF-8");
        System.out.println("收到请求：" + re);

        // 回应客户端
        ByteBuffer writeBuffer = ByteBuffer.wrap(("我已经收到你的请求，你的请求内容是：" + re).getBytes());
        socketChannel.write(writeBuffer);

        buffer.clear();
      }
    } catch (IOException e) {
      IOUtils.closeQuietly(socketChannel);
    }
  }
}
```

- 客户端

```java
public class SocketChannelTest {
  public static void main(String[] args) throws IOException {
    SocketChannel socketChannel = SocketChannel.open();
    socketChannel.connect(new InetSocketAddress("localhost", 8080));

    // 发送请求
    ByteBuffer buffer = ByteBuffer.wrap("1234567890".getBytes());
    socketChannel.write(buffer);

    // 读取响应
    ByteBuffer readBuffer = ByteBuffer.allocate(1024);
    int num;
    if ((num = socketChannel.read(readBuffer)) > 0) {
      readBuffer.flip();

      byte[] re = new byte[num];
      readBuffer.get(re);

      String result = new String(re, "UTF-8");
      System.out.println("返回值: " + result);
    }
  }
}
```

- 来一个新的连接，就新开一个线程来处理这个连接，之后的操作全部由那个线程来完成
- 性能瓶颈
  - 每次来一个连接都开一个新的线程这肯定是不合适的，当活跃连接数在几十几百的时候当然是可以这样做的，但如果活跃连接数是几万几十万的时候，这么多线程明显就不行了。每个线程都需要一部分内存，内存会被迅速消耗，同时，线程切换的开销非常大。
  - 阻塞操作在这里也是一个问题。首先，accept( ) 是一个阻塞操作，当 accept( ) 返回的时候，代表有一个连接可以使用了，马上就新建线程来处理这个 SocketChannel 了，但是这里不代表对方就将数据传输过来了。所以，SocketChannel#read 方法将阻塞，等待数据，明显这个等待是不值得的。同理，write 方法也需要等待通道可写才能执行写入操作，这边的阻塞等待也是不值得的。



#### 非阻塞 IO

- NIO、Non-blocking / New IO

- 核心在于使用一个 Selector 来管理多个通道，可以是 SocketChannel，也可以是 ServerSocketChannel，将各个通道注册到 Selector 上，指定监听的事件

- 可以只用一个线程来轮询这个 Selector，看看是否有通道是准备好的，当通道准备好可读或可写，然后才去开始真正的读写，这样速度就很快了。这样就没有必要给每个通道都起一个线程

- NIO 中 Selector 是对底层操作系统实现的一个抽象，管理通道状态其实都是底层系统实现的

  - select

    - 上世纪 80 年代就实现了
    - 支持注册 FD_SETSIZE(1024) 个 socket，在那个年代肯定是够用的，不过现在肯定是不行
    - 时间复杂度 O(n)

  - poll

    - 1997 年，出现了 poll 作为 select 的替代者

    - poll 不再限制 socket 数量

    - 时间复杂度 O(n)

      select 和 poll 都有一个共同的问题，那就是它们都只会告诉你有几个通道准备好了，但是不会告诉你具体是哪几个通道。所以，一旦知道有通道准备好以后，自己还是需要进行一次扫描，显然这个不太好，通道少的时候还行，一旦通道的数量是几十万个以上的时候，扫描一次的时间都很可观了，时间复杂度 O(n)

  - epoll

      - 2002 年随 Linux 内核 2.5.44 发布
      - epoll 能直接返回具体的准备好的通道，时间复杂度 O(1)

- JVM 就是这么一个屏蔽底层实现的平台，面向 Selector 编程就可以了

```java
public class SelectorServer {

  public static void main(String[] args) throws IOException {
    Selector selector = Selector.open();

    ServerSocketChannel server = ServerSocketChannel.open();
    server.socket().bind(new InetSocketAddress(8080));

    // 将其注册到 Selector 中，监听 OP_ACCEPT 事件
    server.configureBlocking(false);
    server.register(selector, SelectionKey.OP_ACCEPT);

    while (true) {
      int readyChannels = selector.select();
      if (readyChannels == 0) {
        continue;
      }
      Set<SelectionKey> readyKeys = selector.selectedKeys();
      // 遍历
      Iterator<SelectionKey> iterator = readyKeys.iterator();
      while (iterator.hasNext()) {
        SelectionKey key = iterator.next();
        iterator.remove();

        if (key.isAcceptable()) {
          // 有已经接受的新的到服务端的连接
          SocketChannel socketChannel = server.accept();

          // 有新的连接并不代表这个通道就有数据，
          // 这里将这个新的 SocketChannel 注册到 Selector，监听 OP_READ 事件，等待数据
          socketChannel.configureBlocking(false);
          socketChannel.register(selector, SelectionKey.OP_READ);
        } else if (key.isReadable()) {
          // 有数据可读
          // 上面一个 if 分支中注册了监听 OP_READ 事件的 SocketChannel
          SocketChannel socketChannel = (SocketChannel) key.channel();
          ByteBuffer readBuffer = ByteBuffer.allocate(1024);
          int num = socketChannel.read(readBuffer);
          if (num > 0) {
            // 处理进来的数据...
            System.out.println("收到数据：" + new String(readBuffer.array()).trim());
            ByteBuffer buffer = ByteBuffer.wrap("返回给客户端的数据...".getBytes());
            socketChannel.write(buffer);
          } else if (num == -1) {
            // -1 代表连接已经关闭
            socketChannel.close();
          }
        }
      }
    }
  }
}
```



#### 异步 IO

- AIO、Asynchronous I/O、More New IO

- 也称 NIO.2，随 JDK 1.7 发布，引入了异步 IO 接口和 Paths 等文件访问接口

- 异步场景

  - 通常，有一个线程池用于执行异步任务，提交任务的线程将任务提交到线程池就可以立马返回，不必等到任务真正完成
  - 如果想要知道任务的执行结果，通常是通过传递一个回调函数的方式，任务结束后去调用这个函数

- Java 中的异步 IO，都是由一个线程池来负责执行任务，然后使用回调或自己去查询结果

- 异步 IO 主要是为了控制线程数量，减少过多的线程带来的内存消耗和 CPU 在线程调度上的开销

- Java 异步 IO 提供了两种使用方式

  - 返回 Future 实例
  - 使用回调函数

- 返回 Future 实例

  - java.util.concurrent.Future 
  - future.isDone( )
    - 判断操作是否已经完成，包括了正常完成、异常抛出、取消
  - future.cancel(true)
    - 取消操作，方式是中断
    - 参数 true 表示，即使这个任务正在执行，也会进行中断
  - future.isCancelled( )
    - 是否被取消，只有在任务正常结束之前被取消，这个方法才会返回 true
  - future.get( )
    - 获取执行结果，阻塞
  - future.get(10, TimeUnit.SECONDS)
    - 如果上面的 get( ) 的阻塞不满意，就设置超时时间

- 使用回调函数

  - java.nio.channels.CompletionHandler

  ```java
  public interface CompletionHandler<V,A> {
    void completed(V result, A attachment);
  
    void failed(Throwable exc, A attachment);
  }
  ```

  ```java
  AsynchronousServerSocketChannel listener = AsynchronousServerSocketChannel.open().bind(null);
  
  // accept 方法的第一个参数可以传递 attachment
  listener.accept(attachment, new CompletionHandler<AsynchronousSocketChannel, Object>() {
    public void completed(
      AsynchronousSocketChannel client, Object attachment) {
      // 
    }
    
    public void failed(Throwable exc, Object attachment) {
      // 
    }
  });
  ```



#### AsynchronousFileChannel

- 异步的文件 IO
- 文件 IO 在所有的操作系统中都不支持非阻塞模式，但是可以对文件 IO 采用异步的方式来提高性能
- 文件开始位置为 0

```java
AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths.get("/Users/mac/test.txt"));

ByteBuffer buffer = ByteBuffer.allocate(1024);
// Future
Future<Integer> result = channel.read(buffer, 0);
// 回调
public abstract <A> void read(ByteBuffer dst, long position, A attachment, CompletionHandler<Integer,? super A> handler);
```

```java
public abstract Future<Integer> write(ByteBuffer src, long position);

public abstract <A> void write(ByteBuffer src, long position, A attachment, CompletionHandler<Integer,? super A> handler);
```

- 将内存中的数据刷入到磁盘的方法
  - 对文件的写操作，操作系统并不会直接针对文件操作，系统会缓存，然后周期性地刷入到磁盘
  - 如果希望将数据及时写入到磁盘中，以免断电引发部分数据丢失，可以调用此方法
  - 参数如果设置为 true，意味着同时也将文件属性信息更新到磁盘

```java
public abstract void force(boolean metaData) throws IOException;
```

- 对文件的锁定的方法
  - 可以锁定文件的部分数据，进行排他性的操作
  - position 是要锁定内容的开始位置
  - size 指示了要锁定的区域大小
  - shared 指示需要的是共享锁还是排他锁

```java
public abstract Future<FileLock> lock(long position, long size, boolean shared);

public abstract <A> void lock(long position, long size, boolean shared, A attachment, CompletionHandler<FileLock,? super A> handler);

// 这个方法就是尝试去获取锁，如果该区域已被其他线程或其他应用锁住，那么立刻返回 null，否则返回  FileLock 对象
public abstract FileLock tryLock(long position, long size, boolean shared) throws IOException;
```



### AsynchronousServerSocketChannel

- 对应的是非阻塞 IO 的 ServerSocketChannel

```java
package com.javadoop.aio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.SocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousServerSocketChannel;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.channels.CompletionHandler;

public class Server {

  public static void main(String[] args) throws IOException {

    // 实例化，并监听端口
    AsynchronousServerSocketChannel server = AsynchronousServerSocketChannel.open().bind(new InetSocketAddress(8080));

    // 自己定义一个 Attachment 类，用于传递一些信息
    Attachment att = new Attachment();
    att.setServer(server);

    server.accept(att, new CompletionHandler<AsynchronousSocketChannel, Attachment>() {
      @Override
      public void completed(AsynchronousSocketChannel client, Attachment att) {
        try {
          SocketAddress clientAddr = client.getRemoteAddress();
          System.out.println("收到新的连接：" + clientAddr);

          // 收到新的连接后，server 应该重新调用 accept 方法等待新的连接进来
          att.getServer().accept(att, this);

          Attachment newAtt = new Attachment();
          newAtt.setServer(server);
          newAtt.setClient(client);
          newAtt.setReadMode(true);
          newAtt.setBuffer(ByteBuffer.allocate(2048));

          // 这里也可以继续使用匿名实现类，不过代码不好看，所以这里专门定义一个类
          client.read(newAtt.getBuffer(), newAtt, new ChannelHandler());
        } catch (IOException ex) {
          ex.printStackTrace();
        }
      }

      @Override
      public void failed(Throwable t, Attachment att) {
        System.out.println("accept failed");
      }
    });
    // 为了防止 main 线程退出
    try {
      Thread.currentThread().join();
    } catch (InterruptedException e) {
    }
  }
}
```

```java
package com.javadoop.aio;

import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.CompletionHandler;
import java.nio.charset.Charset;

public class ChannelHandler implements CompletionHandler<Integer, Attachment> {

  @Override
  public void completed(Integer result, Attachment att) {
    if (att.isReadMode()) {
      // 读取来自客户端的数据
      ByteBuffer buffer = att.getBuffer();
      buffer.flip();
      byte bytes[] = new byte[buffer.limit()];
      buffer.get(bytes);
      String msg = new String(buffer.array()).toString().trim();
      System.out.println("收到来自客户端的数据: " + msg);

      // 响应客户端请求，返回数据
      buffer.clear();
      buffer.put("Response from server!".getBytes(Charset.forName("UTF-8")));
      att.setReadMode(false);
      buffer.flip();
      // 写数据到客户端也是异步
      att.getClient().write(buffer, att, this);
    } else {
      // 到这里，说明往客户端写数据也结束了，有以下两种选择:
      // 1. 继续等待客户端发送新的数据过来
      //            att.setReadMode(true);
      //            att.getBuffer().clear();
      //            att.getClient().read(att.getBuffer(), att, this);
      // 2. 既然服务端已经返回数据给客户端，断开这次的连接
      try {
        att.getClient().close();
      } catch (IOException e) {
      }
    }
  }

  @Override
  public void failed(Throwable t, Attachment att) {
    System.out.println("连接断开");
  }
}
```

- 自定义的 Attachment 类，用于传递一些信息

```java
public class Attachment {
    private AsynchronousServerSocketChannel server;
    private AsynchronousSocketChannel client;
    private boolean isReadMode;
    private ByteBuffer buffer;
    // getter & setter
}
```



### AsynchronousSocketChannel

```java
package com.javadoop.aio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.charset.Charset;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Client {

  public static void main(String[] args) throws Exception {
    AsynchronousSocketChannel client = AsynchronousSocketChannel.open();
    // 来个 Future 形式的
    Future<?> future = client.connect(new InetSocketAddress(8080));
    // 阻塞一下，等待连接成功
    future.get();

    Attachment att = new Attachment();
    att.setClient(client);
    att.setReadMode(false);
    att.setBuffer(ByteBuffer.allocate(2048));
    byte[] data = "I am obot!".getBytes();
    att.getBuffer().put(data);
    att.getBuffer().flip();

    // 异步发送数据到服务端
    client.write(att.getBuffer(), att, new ClientChannelHandler());

    // 这里休息一下再退出，给出足够的时间处理数据
    Thread.sleep(2000);
  }
}
```

```java
package com.javadoop.aio;

import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.CompletionHandler;
import java.nio.charset.Charset;

public class ClientChannelHandler implements CompletionHandler<Integer, Attachment> {

  @Override
  public void completed(Integer result, Attachment att) {
    ByteBuffer buffer = att.getBuffer();
    if (att.isReadMode()) {
      // 读取来自服务端的数据
      buffer.flip();
      byte[] bytes = new byte[buffer.limit()];
      buffer.get(bytes);
      String msg = new String(bytes, Charset.forName("UTF-8"));
      System.out.println("收到来自服务端的响应数据: " + msg);

      // 接下来，有以下两种选择:
      // 1. 向服务端发送新的数据
      //            att.setReadMode(false);
      //            buffer.clear();
      //            String newMsg = "new message from client";
      //            byte[] data = newMsg.getBytes(Charset.forName("UTF-8"));
      //            buffer.put(data);
      //            buffer.flip();
      //            att.getClient().write(buffer, att, this);
      // 2. 关闭连接
      try {
        att.getClient().close();
      } catch (IOException e) {
      }
    } else {
      // 写操作完成后，会进到这里
      att.setReadMode(true);
      buffer.clear();
      att.getClient().read(buffer, att, this);
    }
  }

  @Override
  public void failed(Throwable t, Attachment att) {
    System.out.println("服务器无响应");
  }
}
```



### Asynchronous Channel Groups

- 异步 IO 一定存在一个线程池，这个线程池负责接收任务、处理 IO 事件、回调等，这个线程池就在 group 内部，group 一旦关闭，那么相应的线程池就会关闭
- AsynchronousServerSocketChannels 和 AsynchronousSocketChannels 是属于 group 的，当调用它们的 open( ) 时，相应的 channel 就属于默认的 group，这个 group 由 JVM 自动构造并管理
- 配置这个默认的 group，可以在 JVM 启动参数中指定系统变量
  - java.nio.channels.DefaultThreadPool.threadFactory
    - 此系统变量用于设置 ThreadFactory，它应该是 java.util.concurrent.ThreadFactory 实现类的全限定类名
    - 一旦指定了这个 ThreadFactory 以后，group 中的线程就会使用该类产生
  - java.nio.channels.DefaultThreadPool.initialSize
    - 用于设置线程池的初始大小
- 自定义 group
  - AsynchronousChannelGroup.withCachedThreadPool(ExecutorService executor, int initialSize)
  - AsynchronousChannelGroup.withFixedThreadPool(int nThreads, ThreadFactory threadFactory)
  - AsynchronousChannelGroup.withThreadPool(ExecutorService executor)
- 使用

```java
AsynchronousChannelGroup group = AsynchronousChannelGroup.withFixedThreadPool(10, Executors.defaultThreadFactory());
AsynchronousServerSocketChannel server = AsynchronousServerSocketChannel.open(group);
AsynchronousSocketChannel client = AsynchronousSocketChannel.open(group);
```

