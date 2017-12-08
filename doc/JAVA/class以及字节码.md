jdk源码分析--了解class文件

class文件结构
其实一般来说，如果仅仅是读一些常用类库的源代码的话，是用不到了解字节码、class文件结构等相关内容的，不过有些java内部的实现机制需要经过反编译之后我们才能够知道原理，反编译后的代码去掉了一些表层的语法，更容易看到java底层的实现原理，而要看懂反编译的代码就需要了解一些字节码相关知识。这部分知识在 深入了解java虚拟机 一书中有详细的解释。
class文件是以8位(BYTE)为基本单位的二进制流。中间没有分隔符，其中有两种数据类型：无符号数，表
无符号数：以u1 u2 u4 u8 代表1 2 4 8个字节的无符号数。可以描述数字，索引引用，数量值，utf-8编码的字符串值。
表：以_info结尾，由多个无符号数和表构成的复合数据结构
一个class文件实际就是一张大表，结构是固定的，依次如下：
1 magic number(u4)：魔数，值用16进制表示--0xCAFEBABE，表明这是个java class文件
2 minor version, major version(u2 * 2)：jdk版本，主次版本各占两个字节，譬如：major:52对应jdk8，次版本0，那么编译器版本号就是1.8.0
3 constant_pool_count(u2): 表示常量池中有多少常量。
4 Constant pool(cp_info):常量池保存两种类常量--字面量和符号引用。
字面量：文本字符串，final常量值；符号引用：类、接口全限定名，字段名称和描述符，方法名称和描述符。
关于符号引用是干啥的：java编译不会像c语言那样有连接这一步，而是在加载class时动态连接--也就是说一个class文件中不会保存其他类，方法的结构或者说内存布局。
常量池中目前有14种类型的常量，不同常量类型的结构也不同，不过相同点是：第一位是u1类型的标志位，表明是哪种类型的常量
关于常量池了解这么多就可以了，具体的常量类型可以用的时候去查。
5 access_flags(u2):定义了8个标志位，一共可以定义16个：public final interface abstract enum annotation等，其中有个acc_super，它的意思是使用心得invokespecial语义，一般调用构造方法，或显示使用super调用父类方法时，会用到这个指令，动态绑定父类方法。
6 this_class(u2): 类索引，索引指向类全限定名
7 super_class(u2): 父类索引，指向全限定名
8 interface_count(u2): 接口数量
9 interfaces(u2 * n): 一组接口索引，指向全限定名
10 fields_count(u2): 字段数量
11 fields_info(field_info): 类中声明的变量，不包括方法内部的局部变量。
12 methods_count(u2): 方法数量
13 methods(method_info): 类中声明的方法
14 attributes_count(u2): 属性数量
15 attributes(attribute_info): 属性表，定义一些专有的属性，供上面的一些表使用

字节码指令
java虚拟机指令由一个字节长度代表特定操作含义的数字（操作码），以及0-多个所需参数（操作数）构成。java虚拟机采用面向操作数栈而不是寄存器架构，所以很多指令都不含操作数。（书上原话照抄 - -！）
大多数指令名称会包含对应操作数的数据类型，如iload用于从局部变量表中加载int型数据到操作数栈，一般缩写：i--int l--long s--short b--byte c--char f--float d--double a--reference
一些常用的指令简单了解下：
1、加载和存储
将局部变量加载到操作栈：?load
将数值从操作栈存储到局部变量表：?store
将常量加载到操作栈：?push ldc ?const
2、运算指令，主要有整型和浮点型的运算，不直接支持byte short char boolean，这些可以使用int指令代替
加减乘除：?add ?sub ?mul ?div
求余：?rem 取反：?neg. 还有移位、与或等等吧
3、类型转换。java虚拟机直接支持小范围类型到大范围类型的转换
4、对象创建和访问
创建实例：new
创建数组：newarray，anewarray等
访问类变量、实例变量：getfield putfield getstatic putstatic
取数组长度：arraylength
检查实例类型：instanceof checkcast
5、操作操作数栈：
栈顶元素出栈：pop
6、控制转移指令：
条件分支：if?
复合条件分支：tableswitch lookupswitch
无条件分支：goto goto_w jsr等
7、方法调用与返回
调用对象实例方法：invokevirtual invoke?等
返回指令：?return
8、异常抛出：athrow，异常处理不是由	字节码指令实现的
9、同步：方法级同步是隐式的，实现在方法调用和返回中。不过方法级别和方法内部级别同步都是通过管程--monitor来实现


其他补充：
1、在字段表和方法表的数据结构中有：描述符，简单名称的概念。其中简单名称就是平时看到的方法名，字段名。描述符要复杂些：它需要描述字段的数据类型，方法的参数列表和返回值。当描述数据类型时：基本类型以及void都有一个大写字母表示，对象类型使用L加全限定名表示，数组[表示。
2、表中可以引用其他类型表的实例来描述。譬如常量池中会定义很多CONSTANT_utf8_info类型的常量，供其他表中使用。
3、我们一般使用javap命令对代码进行反编译，反编译后得到的代码不会是16进制数，其实比较好读，但是要了解大概的文件结构。
4、invokevirtual指令用于调用对象的实例方法，根据对象的实际类型进行分派。invokespecial调用一些需要特殊处理的实例方法，包括实例初始化方法，私有方法以及类方法，具体了解在java虚拟机第八章。
