这本书很棒棒，学中间件这本书可以多看看

1、分布式系统：组件分布在网络计算机上，组件间通过消息来通信并协调行动
2、并发的一些知识点：
--多进程与多线程：之前讨论大部分情况都是单进程中的多线程，这些线程共享内存空间，共享数据时会存在同步的问题；多个进程之间的内存空间是独立的。
--线程池，与每次都创建新线程相比，线程池可以降低创建线程的开销，
--ReetrantLock，可重入锁。使用方法和synchronized修饰代码块的时候类似，不过要在用完后显示的unlock释放锁。看起来使用synchronized更安全，为何要使用这个锁：1 提供trylock方法，该方法可以判断锁是否被其他线程占有，可以立即返回。 2 可以构造公平锁和非公平锁。 3 提供ReentrantReadWriteLock 适用于读不互斥读多写少的场景。简单使用场景：
lock.lock();/lock.readLock();/lock.writeLock();
try{} finally {lock.unlock();}
--其他CountDownLatch，CyclicBarrier，SemaPhore，Exchanger，FutureTask可以后面详细学
3、网络io：目前主要接触的网络模型是tcp协议栈。当使用socket进行网络开发时实现方式有三种：bio，nio，aio
bio：一个socket套接字需要使用一个线程来进行处理，发生连接，读写数据都会阻塞线程。这种模式编程简单，主要问题是一个线程只处理一个socket，如果server端要支持多并发，就要支持多线程。
nio：jdk1.4 事件驱动，采用reactor模式，一个线程处理多个socket，然后派发到不同线程中
aio：jdk1.7 异步io，采用proactor模式，与nio区别是：aio进行读写时，调用read write方法，并传入completeHandler（动作完成后处理器），在完成读写后，会调用handler
4、三种基本中间件：远程调用和对象访问；消息；数据访问
5、java虚拟机：有多种产品（实现）oracle hotspot，microsoft jvm，bea jrocket被oracle收购了；其中hotspot应用最广泛。
6、rpc框架部分介绍很详细，可以直接看书
