1 io
输出：print "Python"
输入：
raw_input([prompt]) 函数从标准输入读取一个行，并返回一个字符串（去掉结尾的换行符）
input([prompt]) 函数和 raw_input([prompt]) 函数基本类似，但是 input 可以接收一个Python表达式作为输入，并将运算结果返回。

打开和关闭文件：
open 函数
你必须先用Python内置的open()函数打开一个文件，创建一个file对象，相关的方法才可以调用它进行读写。
file object = open(file_name [, access_mode][, buffering])
File对象的用法：todo

2 异常处理
捕捉异常可以使用try/except语句。try/except语句用来检测try语句块中的错误，从而让except语句捕获异常信息并处理。
    try:
        return int(var)
    except ValueError, Argument:
        print "参数没有包含数字\n", Argument
使用raise语句自己触发异常：raise Exception("Invalid level!", level)

3 类：
class ClassName:
   '类的帮助信息'   #类文档字符串
   class_suite  #类体
例子：
class Employee:
   '所有员工的基类'
   empCount = 0
 
   def __init__(self, name, salary):
      self.name = name
      self.salary = salary
      Employee.empCount += 1
   
   def displayCount(self):
     print "Total Employee %d" % Employee.empCount

__init__()方法是一种特殊的方法，被称为类的构造函数或初始化方法，当创建了这个类的实例时就会调用该方法
empCount 变量是一个成员变量，它的值将在这个类的所有实例之间共享。在内部使用：self.empCount
self 代表类的实例，self 在定义类的方法时是必须有的，虽然在调用时不必传入相应的参数。命名不一定叫self，但必须是第一个参数。
另外如果变量或者方法前面有两个下划线，表示是私有的，只能在类内部使用，外部不可用。但实际是可以通过增加类名进行访问。

继承：
class A:        # 定义类 A
class B:         # 定义类 B
class C(A, B):   # 继承类 A 和 B

实例化：
a = A()




