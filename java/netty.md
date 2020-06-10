# netty

可用三种线程模型开发

单线程模型  多线程模型（一个线程接受，一个线程池处理）  主从线程模型（两个线程池）

`EventLoopGroup`是线程组类。

`ServerBootstrap`是启动类，装载线程组并设置通道类型和子处理器handler。



