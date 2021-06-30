---
title: NIO—通道(Channel)的原理与获取
tags:
  - [NIO]
categories:
  - [NIO]
comments: true
description: NIO—通道(Channel)的原理与获取
date: 2021-06-30 12:07:21
top:
---
## 一、通道(Channel)

> 用于源节点与目标节点的连接，在Java NIO 中负责缓冲区中数据的传输，Channel 本身不存储数据，因此需要配合缓冲区进行传输。



## 二、通道的主要实现类

java.nio.channels.Channel 接口：

​	|--FileChannel

​	|--SocketChannel

​	|--ServerSocketChannel

​	|--DatagramChannel



## 三、获取通道

1. Java 针对支持通道的类提供了getChannel() 方法
   - 本地IO：
     - FileInputStream/FileOutputStream
     - RandomAccessFile
   - 网络IO：
     - Socket
     - ServerSocket
     - DatagramSocket
2. 在JDK 1.7 中的NIO.2 针对各个通道提供了静态方法 open()
3. 在JDK 1.7 中的NIO.2 的Files 工具类的 newByteChannel()



## 四、通道之间的数据传输

### 1.利用通道完成文件的复制

```java
FileInputStream fis=new FileInputStream("D:\\test0.png");
FileOutputStream fos=new FileOutputStream("D:\\test2.png");
// 获取通道
FileChannel inChannel=fis.getChannel();
FileChannel outChannel = fos.getChannel();

//分配指定大小的缓冲区
ByteBuffer buf=ByteBuffer.allocate(1024);

//将通道中的数据存入缓冲区
while(inChannel.read(buf)!=-1){
    buf.flip();//切换读取数据的模式
    //将缓冲区中的数据写入通道中
    outChannel.write(buf);
    buf.clear();//清空缓冲区
}

outChannel.close();
inChannel.close();
fos.close();
fis.close();
```

### 2.使用直接缓冲区完成文件的复制(内设映射文件)

```java

FileChannel inChannel = FileChannel.open(Paths.get("D:\\test0.png"), StandardOpenOption.READ);
FileChannel outChannel = FileChannel.open(Paths.get("D:\\test2.png"), StandardOpenOption.WRITE,StandardOpenOption.READ,StandardOpenOption.CREATE_NEW);
//内存映射文件
MappedByteBuffer inMappedBuf = inChannel.map(FileChannel.MapMode.READ_ONLY, 0, inChannel.size());
MappedByteBuffer outMappedBuf = outChannel.map(FileChannel.MapMode.READ_WRITE, 0, inChannel.size());
//直接对缓冲区进行数据的读写操作
byte[] dst=new byte[inMappedBuf.limit()];
inMappedBuf.get(dst);
outMappedBuf.put(dst);

inChannel.close();
outChannel.close();
```

### 3.通道之间的数据传输

```java

FileChannel inChannel = FileChannel.open(Paths.get("D:\\test0.png"), StandardOpenOption.READ);
FileChannel outChannel = FileChannel.open(Paths.get("D:\\test2.png"), StandardOpenOption.WRITE,StandardOpenOption.READ,StandardOpenOption.CREATE_NEW);

inChannel.transferTo(0,inChannel.size(),outChannel);
//outChannel.transferFrom(inChannel,0,inChannel.size());

inChannel.close();
outChannel.close();
```



## 五、分散（Scatter）与聚集（Gather）

- 分散读取(Scattering Reads) ： 将通道中的数据分散到多个缓冲区中
- 聚集写入(Gathering Writes)： 将多个缓冲区中的数据聚集到通道中。

```java
//分散和聚集
RandomAccessFile raf=new RandomAccessFile("D:\\test0.png","rw");
//1.获取通道
FileChannel channel = raf.getChannel();
//2.分配指定大小的缓冲区
ByteBuffer buf1 = ByteBuffer.allocate(100);
ByteBuffer buf2 = ByteBuffer.allocate(1024);
//3.分散读取
ByteBuffer[] bufs={buf1,buf2};
channel.read(bufs);
for (ByteBuffer buf : bufs) {
    buf.flip();
}
System.out.println(new String(bufs[0].array(),0,bufs[0].limit()));
System.out.println(new String(bufs[1].array(),0,bufs[1].limit()));

//聚集写入
RandomAccessFile raf2 = new RandomAccessFile("D:\\test2.png", "rw");
FileChannel channel1 = raf2.getChannel();
channel1.write(bufs);
channel.close();
channel1.close();
```



## 六、字符集Charset

编码：字符串-》字节数组

解码：字节数组-》字符串

###  1.字符集

```java

SortedMap<String, Charset> map = Charset.availableCharsets();
Set<Map.Entry<String, Charset>> set = map.entrySet();
for (Map.Entry<String, Charset> entry : set) {           	System.out.println(entry.getKey()+"="+entry.getValue());
}

Charset gbk = Charset.forName("GBK");
//获取编码器和解码器
CharsetEncoder en = gbk.newEncoder();
CharsetDecoder de = gbk.newDecoder();
CharBuffer cBuf = CharBuffer.allocate(1024);
cBuf.put("测试！");
cBuf.flip();
//编码
ByteBuffer encode = en.encode(cBuf);
for(int i=0;i<6;i++){
    System.out.println(encode.get());
}
//解码
encode.flip();
CharBuffer decode = de.decode(encode);
System.out.println(decode.toString());
```





