commons-logging(interface) + log4j(impl)
slf4j(interface) + logback(impl)
slf4j + log4j

commons-logging是一个接口包
使用日志时，经常这样使用：private final static Log log = LogFactory.getLog(xxx.class);
内部代码结构很简单，org.apache.commons.logging中
Log接口，定义了日志的各种记录方法
Exception类
LogFactory实现，
impl中一系列Log的实现。
LogFactory中的getLog方法是一个抽象方法，没有具体实现。。代码实现没有依赖任何log4j，具体是在使用时调用log4j代码的？？

在getFactory中决定实现类

首先获取classLoader，为何使用classloader，因为他要动态加载log的实现类。
获取classloader有几种方法：
其一Java 1.2以及之后：
Method method = Thread.class.getMethod("getContextClassLoader", null);
ClassLoader cl = (ClassLoader) method.invoke(Thread.currentThread(), null);
其二：ClassLoader cl = className.class.getClassLoader();
其三：Thread.currentThread().getContextClassLoader()

使用了第一种。

// TODO 先看到者，后面有时间在继续研究
