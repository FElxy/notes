通过npm安装的Node.js模块主要分为以下两种。
〇 普通模块：提供API调用。
〇 二进制模块：命令行工具，供CLI调用。

基于Yargs库解析process.argv, 设置参数

Node.js是以单线程的模式运行的，但它使用事件驱动来处理并发，这样有助于我们在多核CPU的系统上创建多个子进程，从而提高性能。每个子进程中都含有3个流对象：child.stdin、child.stdout和child.stderr。它们可能会共享父进程的stdio流，也可以作为独立的流对象被导流。
Node.js提供了child_process模块可用于创建子进程，方法有3种，具体如下。
〇 exec-child_process.exec：使用子进程执行命令，缓存子进程的输出，并将子进程的输出以回调函数参数的形式一次性返回。
〇 spawn-child_process.spawn：使用指定的命令行参数创建新的子进程。当希望子进程向Node.js父进程返回大量数据时，比如进行图像处理、读取二进制数据等，最好使用spawn方法。
〇 fork-child_process.fork:spawn方法的特殊形式，fork用于在子进程中运行模块，例如，fork("\./son.js")相当于spawn("node",["./son.js"])。与spawn方法不同的是，fork方法会在父进程与子进程之间建立一个通信管道，用于进程间通信。


LRU(Least Recently Used)的意思是“最近最少使用”，是在有限内存下实现缓存价值最大化的安全算法。假设我们要缓存一定量的数据，当数据量超过设定的阈值时，就需要将一些过期的数据删除。比如我们要缓存10000条数据，当数据量小于10000条时可以随意添加，当数据量超过10000条时，如需添加新的数据，要将过期数据删掉，以确保最大缓存量是10000条。那怎么确定删除哪条过期数据呢？这时就要用到LRU算法，保留最新的数据，删掉最近最少使用的数据。