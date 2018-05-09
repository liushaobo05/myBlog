---
title: 聊一聊node.js
date: 2018-05-03 13:49:03
categories: node.js
tags:
---
接触node.js有三年有余了，期间也参与过一些大的node项目，但回过头，感觉还是需要对这个编程框架好好的整理下，已达到深入的地方。

#### node.js结构
![image](http://img4.tbcdn.cn/L1/461/1/a9e67142615f49863438cc0086b594e48984d1c9)

由上图可以看出,node.js分三层
1.Node.js 标准库，这部分是由 Javascript 编写的，即我们使用过程中直接能调用的 API。在源码中的 lib 目录下可以看到

2.Node bindings，这一层是 Javascript 与底层 C/C++ 能够沟通的关键，前者通过 bindings 调用后者，相互交换数据。实现在 node.cc

3. 最底层是node.js运行的核心。
- V8支撑了js的运行时，提供了js高效的执行环境
- Libuv：它为 Node.js 提供了跨平台，线程池，事件池，异步 I/O 等能力，是 Node.js 主要的特点

重点对libuv展开
![image](http://img1.tbcdn.cn/L1/461/1/cdd725280cce7929c5dd526bbf19e9de36591e09)
它是一个对开发者友好的工具集，包含定时器，非阻塞的网络 I/O，异步文件系统访问，子进程等功能。它封装了 Libev、Libeio 以及 IOCP，保证了跨平台的通用性,屏蔽了底层的差异性。

先来看一个例子，打开一个文件
```
var fs = require('fs');
fs.open('./test.txt', "w", function(err, fd) {
	//..do something
});
```
代码的调用过程大致可描述为：lib/fs.js → src/node_file.cc → uv_fs
由以上可以得知，node.js通过V8将Javascript解释,然后由 C/C++ 来执行真正的系统调用,可以看出Node.js 并不是一门语言，而是一个平台。

#### 异步、非阻塞I/O
当我们将 I/O 操作的请求传达给 Libuv 之后，Libuv 开启线程来执行这次 I/O 调用，并在执行完成后，传回给 Javascript 进行后续处理

 I/O 包括文件 I/O 和 网络 I/O
 ![image](http://img1.tbcdn.cn/L1/461/1/cdd725280cce7929c5dd526bbf19e9de36591e09)
 Libuv 官网的图中，我们可以看到，文件 I/O，DNS 等操作，都是依托线程池（Thread Pool）来实现的。而网络 I/O 这一大类，包括：TCP、UDP、TTY 等，是由 epoll、IOCP、kqueue 来具体实现的。

我们其实对 Node.js 的单线程一直有个误会,事实上，它的单线程指的是自身Javascript 运行环境的单线程，Node.js并没有给 Javascript 执行时创建新线程的能力，最终的实际操作，还是通过 Libuv 以及它的事件循环来执行的。这也就是为什么 Javascript 一个单线程的语言，能在 Node.js 里面实现异步操作的原因，两者并不冲突。

#### 事件驱动
说到，事件驱动，对于前端来说，并不陌生。事件，是一个在 GUI 开发时很常用的一个概念，常见的有鼠标事件，键盘事件等等。在异步的多种实现中，事件是一种比较容易理解和实现的方式。

#### 一些可能的瓶颈
首先，文件的 I/O 方面，用户代码的运行，事件循环的通知等，是通过 Libuv 维护的线程池来进行操作的，它会运行全部的文件系统操作。既然这样，我们抛开硬盘的影响，对于严谨的 C/C++ 来说，这个线程池一定是有大小限制的。官方默认给出的大小是 4。当然是可以改变的。在启动时，通过设置 UV_THREADPOOL_SIZE 来改变这个值即可。不过，最大也只能是 128，因为这个是涉及到内存占用的。

对于网络 I/O 方面，以 Linux 系统下来说，网络 I/O 采用的是 epoll 这个异步模型。它的优点是采用了事件回调的方式，大大降低了文件描述符的创建（Linux下什么都是文件）。

在每次调用 epoll_wait 时，实际返回的是就绪描述符的数量，根据这个值，去 epoll 指定的数组里面取对应数量的描述符，是一种 内存映射 的方式，减少了文件描述符的复制开销。

上面提到的 epoll 指定的数组，它的大小即可监听的数量大小，它在不同的系统下，有不同的默认值，可见这里 epoll create。

有了大小的限制，还远不够，为了保证运行的稳定，防止你在调用 epoll 函数时，指针越界，导致内存泄漏。还会用到另外一个值 maxevents，它是 epoll_wait 所能处理的最大数量，在调用 epoll_wait 时可以指定。一般情况下小于创建时（epoll_create）的数组大小，当然，也可以设置的比 size 大，不过应该没什么用。可以想到如果就绪的事件很多，超过了 maxevents，那么超出的事件就要等待前面的事件处理完成，才可以继续，可能会导致效率的下降。

在这种情况下，你可能会担心事件会丢失。其实，是不会丢失的，它会通过 ep_collect_ready_items 将这些事件保存在一个队列中，在下一个 epoll_wait 再进行通知。

#### Node.js 不适合做什么
由以上的总结，node.js主线程是单线程的，一旦它进行大量的计算操作，回导致整个事件循环阻塞，所以不建议使用node.js处理cpu密集型计算

参考
[Node.js 探秘：初识单线程的 Node.js](http://taobaofed.org/blog/2015/10/29/deep-into-node-1/)


