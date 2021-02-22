## NIO

#### 简介

- Java NIO 中三大组件 Buffer、Channel、Selector



#### Buffer

- 一个 Buffer 本质上是内存中的一块，可以将数据写入这块内存，之后从这块内存获取数据
- 核心是 ByteBuffer
- 可以将 Buffer 理解为一个数组，IntBuffer、CharBuffer、DoubleBuffer 等分别对应 int[]、char[]、double[] 等
- MappedByteBuffer 用于实现内存映射文件
- 操作 Buffer 和操作数组、类集差不多，只不过大部分时候我们都把它放到了 NIO 的场景里面来使用而已
- Buffer 重要属性：position、limit、capacity
  - capacity
    - 代表这个缓冲区的容量，一旦设定就不可以更改
    - 比如 capacity 为 1024 的 IntBuffer，代表其一次可以存放 1024 个 int 类型的值
    - 一旦 Buffer 的容量达到 capacity，需要清空 Buffer，才能重新写入值
  - position 
    - 初始值是 0，每往 Buffer 中写入一个值，position 就自动加 1，代表下一次的写入位置
    - 读操作的时候也是类似的，每读一个值，position 就自动加 1
    - 从写操作模式到读操作模式切换的时候（**flip**），position 都会归零，这样就可以从头开始读写
  - limit
    - 写模式，limit 代表最大能写入的数据，这个时候 limit 等于 capacity
    - 读模式，limit 等于 Buffer 中实际的数据大小，因为 Buffer 不一定被写满
- 初始化 Buffer
  - 每个 Buffer 实现类都提供了一个静态方法  allocate(int capacity) 帮助快速实例化一个 Buffer

```java
ByteBuffer byteBuf = ByteBuffer.allocate(1024);
IntBuffer intBuf = IntBuffer.allocate(1024);
LongBuffer longBuf = LongBuffer.allocate(1024);
// ...
```

```java
public static ByteBuffer wrap(byte[] array) {
    ...
}
```

- 填充 Buffer
  - 各个 Buffer 类都提供了一些 put 方法用于将数据填充到 Buffer 中，如 ByteBuffer 中的几个 put 方法

```java
// 填充一个 byte 值
public abstract ByteBuffer put(byte b);
// 在指定位置填充一个 int 值
public abstract ByteBuffer put(int index, byte b);
// 将一个数组中的值填充进去
public final ByteBuffer put(byte[] src) {...}
public ByteBuffer put(byte[] src, int offset, int length) {...}
```

- 上述这些方法需要自己控制 Buffer 大小，不能超过 capacity，超过会抛 java.nio.BufferOverflowException 异常
- 对于 Buffer 来说，另一个常见的操作中就是，将来自 Channel 的数据填充到 Buffer 中，在系统层面上，这个操作我们称为读操作，因为数据是从外部（文件或网络等）读到内存中

```java
int num = channel.read(buf);
```

- 提取 Buffer 中的值
  - 通常在说 NIO 的读操作的时候，说的是从 Channel 中读数据到 Buffer 中，对应的是对 Buffer 的写入操作
  - 每写入一个值，position 的值都需要加 1，所以 position 最后会指向最后一次写入的位置的后面一个，如果 Buffer 写满了，那么 position 等于 capacity（position 从 0 开始）
  - 如果要读 Buffer 中的值，需要切换模式，从写入模式切换到读出模式
  - 调用 Buffer 的 flip( )，可以从写入模式切换到读取模式，其实这个方法也就是设置一下 position 和 limit 值

```java
public final Buffer flip() {
    limit = position; // 将 limit 设置为实际写入的数据数量
    position = 0; // 重置 position 为 0
    mark = -1; // mark 之后再说
    return this;
}
```

```java
// 根据 position 来获取数据
public abstract byte get();
// 获取指定位置的数据
public abstract byte get(int index);
// 将 Buffer 中的数据写入到数组中
public ByteBuffer get(byte[] dst)
```

- 除了将数据从 Buffer 取出来使用，更常见的操作是将写入的数据传输到 Channel 中，如通过 FileChannel 将数据写入到文件中，通过 SocketChannel 将数据写入网络发送到远程机器等，这种操作称之为写操作

```java
int num = channel.write(buf);
```

- mark( )
  - 一个常用的属性
  - 用于临时保存 position 的值，每次调用 mark( ) 都会将 mark 设值为当前的 position，便于后续需要的时候使用
  - 比如，在 position 为 5 的时候，先 mark( ) 一下，然后继续往下读，读到第 10 的时候，想重新回到 position 为 5 的地方，只要调一下 reset( )，position 就回到 5 了

```java
public final Buffer mark() {
    mark = position;
    return this;
}
```

```java
public final Buffer reset() {
    int m = mark;
    if (m < 0)
        throw new InvalidMarkException();
    position = m;
    return this;
}
```

- rewind( )
  - 会重置 position 为 0，通常用于重新从头读写 Buffer

```java
public final Buffer rewind() {
    position = 0;
    mark = -1;
    return this;
}
```

- clear( )
  - 有点重置 Buffer 的意思，相当于重新实例化
  - 通常，会先填充 Buffer，然后从 Buffer 读取数据，之后再重新往里填充新的数据，一般在重新填充之前先调用 clear( )
  - clear( ) 并不会将 Buffer 中的数据清空，只不过后续的写入会覆盖掉原来的数据，也就相当于清空了数据

```java
public final Buffer clear() {
    position = 0;
    limit = capacity;
    mark = -1;
    return this;
}
```



#### Channel

- 所有的 NIO 操作始于通道，通道是数据来源或数据写入的目的地

- Channel 类似 IO 中的流，用于读取和写入

- 与 Buffer 打交道

  - 读操作的时候将 Channel 中的数据填充到 Buffer 中
  - 写操作时将 Buffer 中的数据写入到 Channel 中

- FileChannel

  - 文件通道，用于文件的读和写
  - 不支持非阻塞
  - 初始化
  
  ```java
  FileInputStream inputStream = new FileInputStream(new File("/data.txt"));
  FileChannel fileChannel = inputStream.getChannel();
  ```
  
  - 读取文件内容
  
  ```java
  ByteBuffer buffer = ByteBuffer.allocate(1024);
  
  int num = fileChannel.read(buffer);
  ```
  
  - 写入文件内容
  
  ```java
  ByteBuffer buffer = ByteBuffer.allocate(1024);
  buffer.put("随机写入一些内容到 Buffer 中".getBytes());
  // Buffer 切换为读模式
  buffer.flip();
  while(buffer.hasRemaining()) {
      // 将 Buffer 中的内容写入文件
      fileChannel.write(buffer);
  }
  ```

- SocketChannel

  - 理解为 TCP 连接通道，可读可写，也可以理解为 TCP 客户端
  - 打开一个 TCP 连接

  ```java
  SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("https://www.javadoop.com", 80));
  
  // 等同于
  // 打开一个通道
  SocketChannel socketChannel = SocketChannel.open();
  // 发起连接
  socketChannel.connect(new InetSocketAddress("https://www.javadoop.com", 80));
  ```

  - SocketChannel 的读写和 FileChannel 没什么区别，就是操作缓冲区

  ```java
  // 读取数据
  socketChannel.read(buffer);
  
  // 写入数据到网络连接中
  while(buffer.hasRemaining()) {
      socketChannel.write(buffer);   
  }
  ```

- SocketChannel

  - SocketChannel 是 TCP 客户端，ServerSocketChannel 就是对应的服务端
  - 用于监听机器端口，管理从这个端口进来的 TCP 连接

  ```java
  // 实例化
  ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
  // 监听 8080 端口
  serverSocketChannel.socket().bind(new InetSocketAddress(8080));
  
  while (true) {
      // 一旦有一个 TCP 连接进来，就对应创建一个 SocketChannel 进行处理
      SocketChannel socketChannel = serverSocketChannel.accept();
  }
  ```

  - ServerSocketChannel 不和 Buffer 打交道了，因为它并不实际处理数据，一旦接收到请求后，实例化 SocketChannel，之后在这个连接通道上的数据传递它就不管了，因为它需要继续监听端口，等待下一个连接

- DatagramChannel

  - 用于 UDP 连接的接收和发送
  - 监听端口

  ```java
  DatagramChannel channel = DatagramChannel.open();
  channel.socket().bind(new InetSocketAddress(9090));
  
  ByteBuffer buf = ByteBuffer.allocate(48);
  
  channel.receive(buf);
  ```

  - 发送数据

  ```java
  String newData = "New String to write to file..." + System.currentTimeMillis();
  
  ByteBuffer buf = ByteBuffer.allocate(48);
  buf.put(newData.getBytes());
  buf.flip();
  
  int bytesSent = channel.send(buf, new InetSocketAddress("jenkov.com", 80));
  ```



#### Selector

- Selector 建立在非阻塞的基础之上，常说的多路复用在 Java中指的就是它，用于实现一个线程管理多个 Channel
- Selector 是一个抽象类，常用方法

```java
public abstract class Selector implements Closeable {
  ......

    /**
     * 得到一个选择器对象
     */
    public static Selector open() throws IOException {
    return SelectorProvider.provider().openSelector();
  }
  ......

    /**
     * 返回所有发生事件的 Channel 对应的 SelectionKey 的集合，通过
     * SelectionKey 可以找到对应的 Channel
     */
    public abstract Set<SelectionKey> selectedKeys();
  ......

    /**
     * 返回所有 Channel 对应的 SelectionKey 的集合，通过 SelectionKey
     * 可以找到对应的 Channel
     */
    public abstract Set<SelectionKey> keys();
  ......

    /**
     * 监控所有注册的 Channel，当其中的 Channel 有 IO 操作可以进行时，
     * 将这些 Channel 对应的 SelectionKey 找到。参数用于设置超时时间
     */
    public abstract int select(long timeout) throws IOException;

  /**
    * 无超时时间的 select 过程，一直等待，直到发现有 Channel 可以进行
    * IO 操作
    */
  public abstract int select() throws IOException;

  /**
    * 立即返回的 select 过程
    */
  public abstract int selectNow() throws IOException;
  ......

  /**
    * 唤醒 Selector，对无超时时间的 select 过程起作用，终止其等待
    */
    public abstract Selector wakeup();
  ......
}
```

- 开启一个 Selector

```java
Selector selector = Selector.open();
```

- 将 Channel 注册到 Selector 上，Selector 建立在非阻塞模式之上，所以注册到 Selector 的 Channel 必须要支持非阻塞模式，注意 FileChannel 不支持非阻塞

```java
// 将通道设置为非阻塞模式，因为默认都是阻塞模式的
channel.configureBlocking(false);
// 注册
SelectionKey key = channel.register(selector, SelectionKey.OP_READ);
```

- 其中，register 方法的第二个 int 型参数（使用二进制的标记位）用于表明需要监听哪些感兴趣的事件，共有四种事件
  - SelectionKey.OP_READ
    - 对应 00000001，通道中有数据可以进行读取
  - SelectionKey.OP_WRITE
    - 对应 00000100，可以往通道中写入数据
  - SelectionKey.OP_CONNECT
    - 对应 00001000，成功建立 TCP 连接
  - SelectionKey.OP_ACCEPT
    - 对应 00010000，接受 TCP 连接
- 可以同时监听一个 Channel 中的发生的多个事件，比如要监听 ACCEPT 和 READ 事件，那么指定参数为二进制的 00010001 即十进制数值 17 即可
- 注册方法返回值是 SelectionKey 实例，包含了 Channel 和 Selector 信息，也包括了一个叫做 Interest Set 的信息，即设置的感兴趣的正在监听的事件集合
- 调用 select( ) 获取通道信息，用于判断是否有感兴趣的事件已经发生了

```java
Selector selector = Selector.open();

channel.configureBlocking(false);

SelectionKey key = channel.register(selector, SelectionKey.OP_READ);

while(true) {
  // 判断是否有事件准备好
  int readyChannels = selector.select();
  if(readyChannels == 0) continue;

  // 遍历
  Set<SelectionKey> selectedKeys = selector.selectedKeys();
  Iterator<SelectionKey> keyIterator = selectedKeys.iterator();
  while(keyIterator.hasNext()) {
    SelectionKey key = keyIterator.next();

    if(key.isAcceptable()) {
      // a connection was accepted by a ServerSocketChannel.

    } else if (key.isConnectable()) {
      // a connection was established with a remote server.

    } else if (key.isReadable()) {
      // a channel is ready for reading

    } else if (key.isWritable()) {
      // a channel is ready for writing
    }

    keyIterator.remove();
  }
}
```



#### 总结

- Buffer 和数组差不多，它有 position、limit、capacity 几个重要属性。put( ) 一下数据、flip( ) 切换到读模式、然后用 get( ) 获取数据、clear( ) 一下清空数据、重新回到 put( ) 写入数据
- Channel 基本上只和 Buffer 打交道，最重要的接口就是 channel.read(buffer) 和 channel.write(buffer)
- Selector 用于实现非阻塞 IO