服务端Handler实现:

ChannelHandler是所有接口的父接口

```java
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelFutureListener;
import io.netty.channel.ChannelHandler.Sharable;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import io.netty.util.CharsetUtil;

/**
 * 只要要有一个ChannelHandler处理器,实现了服务器对客户端接受的数据的处理,就是业务逻辑
 * <p>
 * ChannelInboundHandlerAdapter 继承了 只要要有一个ChannelHandler处理器
 */

/**
 * 一个ChannelHandler可以被多个Channel安全的共享
 */
@Sharable
public class EchoServerHandler extends ChannelInboundHandlerAdapter {

    /**
     * 接收每条消息
     */
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) {
        ByteBuf in = (ByteBuf) msg;
        System.out.println("Server received: " + in.toString(CharsetUtil.UTF_8));

        // 把客户端发过来的消息,再返回回去
        ctx.write(in);
    }

    /**
     * channel read消费完读取的数据的时候被触发
     */
    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) {
        ctx.writeAndFlush(Unpooled.EMPTY_BUFFER)
                .addListener(ChannelFutureListener.CLOSE);
    }

    /**
     * 操作期间,有异常抛出的时候调用
     */
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {

        // 打印异常追踪
        cause.printStackTrace();

        // 关闭Channel
        ctx.close();
    }
}
```

**服务端引导实现:**

```java
import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;

import java.net.InetSocketAddress;

/**
 * 服务端:引导服务器
 */
public class EchoServer {
    private final int port;

    public EchoServer(int port) {
        this.port = port;
    }

    public static void main(String[] args) throws Exception {
        int port = 8080;
        new EchoServer(port).start();
    }

    public void start() throws Exception {
        // 1.创建EventLoopGroup
        EventLoopGroup group = new NioEventLoopGroup();

        try {

            // 2.ServerBootstrap
            ServerBootstrap b = new ServerBootstrap();
            b.group(group)

                    // 3. 指定所使用的NIO 传输的Channel
                    .channel(NioServerSocketChannel.class)

                    // 4.绑定端口
                    .localAddress(new InetSocketAddress(port))

                    // 5.指定处理器Handler
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        public void initChannel(SocketChannel ch) {
                            ch.pipeline().addLast(new EchoServerHandler());
                        }
                    });


            // 6 异步绑定服务器,调用sync方法阻塞等待直到绑定完成
            ChannelFuture f = b.bind().sync();
            System.out.println(EchoServer.class.getName() + " started and listening for connections on " + f.channel().localAddress());

            // 7.获取Channel的closeFuture,并且阻塞当前线程知道它完成
            f.channel().closeFuture().sync();
        } finally {

            // 8.关闭EventLoopGroup,释放所有资源
            group.shutdownGracefully().sync();
        }
    }
}
```

**客户端**

**客户端Handler实现**

```java
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandler.Sharable;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.util.CharsetUtil;

/**
 * 客户端处理器
 */
@Sharable
public class EchoClientHandler extends SimpleChannelInboundHandler<ByteBuf> {

    /**
     * 和服务成功建立连接的时候调用这个方法
     */
    @Override
    public void channelActive(ChannelHandlerContext ctx) {
        ctx.writeAndFlush(Unpooled.copiedBuffer("Netty rocks!", CharsetUtil.UTF_8));
    }

    /**
     * 从服务器收到消息的时候,调用这个方法
     */
    @Override
    public void channelRead0(ChannelHandlerContext ctx, ByteBuf in) {
        System.out.println( "Client received: " + in.toString(CharsetUtil.UTF_8));
    }

    /**
     * 获取异常信息
     */
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx,Throwable cause) {
        cause.printStackTrace();
        ctx.close();
    }
}
```

**客户端引导实现**

```java
public class EchoClient {
    private final String host;
    private final int port;

    public EchoClient(String host, int port) {
        this.host = host;
        this.port = port;
    }

    public void start()
        throws Exception {
        EventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap b = new Bootstrap();
            b.group(group)
                .channel(NioSocketChannel.class)
                .remoteAddress(new InetSocketAddress(host, port))
                .handler(new ChannelInitializer<SocketChannel>() {
                    @Override
                    public void initChannel(SocketChannel ch)
                        throws Exception {
                        ch.pipeline().addLast(
                             new EchoClientHandler());
                    }
                });
            ChannelFuture f = b.connect().sync();
            f.channel().closeFuture().sync();
        } finally {
            group.shutdownGracefully().sync();
        }
    }

    public static void main(String[] args)
            throws Exception {
        if (args.length != 2) {
            System.err.println("Usage: " + EchoClient.class.getSimpleName() +
                    " <host> <port>"
            );
            return;
        }

        final String host = args[0];
        final int port = Integer.parseInt(args[1]);
        new EchoClient(host, port).start();
    }
}
```

# 学习

4.1.10 final 版本

