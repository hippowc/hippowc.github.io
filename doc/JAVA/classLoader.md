ClassLoader是用来将java字节码加载到java虚拟机中。
ClassLoader基本职责是根据一个指定类名称，找到或生成对应字节代码，然后从这些字节代码定义一个java类。除此之外，还负责加载java应用所需资源，如图像文件、配置文件等。
java.lnag.ClassLoader
分为系统提供的classloader和开发人员提供的
其中：bootstrap class loader 加载java核心库，不继承classloader，extensions class loader加载java扩展库，systemn class loader 根据classpath加载，一般java应用类都是它加载的，可以通过，ClassLoader.getSystemClassLoader()得到

开发的类加载器的父类是加载该类加载器的类加载器。

还有另外一种类加载器：线程上下文类加载器
contextClassLoader是在jdk2后引入的，使用getContextClassLoader setContextClassLoader设置和获得类加载器，线程集成父线程的加载器，最开始使用的系统类加载器。
为什么会有这种类加载器？之前双亲委派模式不能解决所有类加载的问题。
java提供很多spi供外部开发者实现，而这些实现会通过依赖jar包方式引入进来，并可以通过classpath找到。问题在于：spi接口是java核心库的一部分，有boot classloader加载，而spi的实现一般是system classloader加载，boot classloader无法找到实现类的，同时system classloader也没有机会加载spi接口。而线程上下文类加载器默认是system context classloader(不是 system classloader)，所有类都是用这个classloader进行加载。

另外一种Class.forName常用在加载数据库驱动，没有提供额外的classloader只是调用者。

详细介绍：
https://www.ibm.com/developerworks/cn/java/j-lo-classloader/

使用ClassLoader加载资源：
使用getResource,getResources等方法可以加载classpath中的资源，传入的路径参数是相对于classpath的相对路径，文件要想被加载到就需要放到当前的classpath中。