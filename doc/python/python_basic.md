1 基础
# 表示注释
#!/usr/bin/python 表示文件使用的解释器
#coding:utf-8 表示中文编码

_ 下划线有特殊意义：
_foo 单下划线表示该名称属性为私有，属于惯例，但对解释器有一定影响，譬如import不会引入这类API
__foo 双下划线，python为了防止被子类覆盖，这种变量会重写为_className__foo
__foo__ 前后双下划线，表示python中的特殊变量，是一种惯例，为了尽量不和用户自定义的变量冲突，可以被覆盖

多行语句： \
total = 1 + \
        2;
        
字符串：' " """都可以表示字符串

输入：
raw_input("按下 enter 键退出，其他任意键显示...\n")
输出：
print x

2、变量
赋值：a = b = c = 1; #都被赋值1  a, b, c = 1, 2, 'ok'; #分别赋值为1 2 ok

数据类型：Number 数字，String 字符串， List 列表，Tuple 元组，Dictionary 字典；赋值时不需要声明类型  a = 1; str = 'test';
Number: int long float complex(复数)
String: s = 'ilovepython'; 从左向右从0开始，从右往左从-1开始，s[0] #i; s[1:2] #lo; s + 'test' #字符串连接
List: []表示，列表数据类型可以不同，从左向右从0开始，从右往左从-1开始；+表示拼接两个列表
Tuple：()表示，不能二次赋值，相当于只读列表；操作与列表相同；
Dictionary：{}表示，保存的时key-value对

python是动态类型，但是是强制类型，以上5中类型都是类可以使用type()方法查看变量类型。
譬如：a = 1; b= "str"; a + b会报错。需要转换为相同类型str()等

3 运算符
and 与 x and y，or 或 x or y，not 非 not x；
成员运算符：in， not in #  'a' in ['a', 'b'] 返回True
身份运算符：is is not；判断是否为同一对象引用 与之相对的 == 是判断值是否相等

4 条件语句
if condition: 
    xxx
elif condition:
    xxx
else:
    xxx

5 循环：
while condition:
    xxx

for v in sequence:
    xxx
其中sequence是一个序列，v会循环遍历sequence中的每个值
range()函数，输出一个数字序列，range(3) # [0,1,2]  range[1,4] #[1,2,3]

6 函数
def funName( params ):
    xxx
支持可变参数，支持参数设置默认值

匿名函数：lambda arg1, arg2..: 表达式； 主体是一个表达式，而不是一个代码块。仅仅能在lambda表达式中封装有限的逻辑进去，可以
作为变量传递。

7 模块
模块(Module)，是一个 Python 文件，以 .py 结尾，模块能定义函数，类和变量，模块里也能包含可执行的代码。

模块引入：
import module1[, module2[,... moduleN]
解释器遇到 import 语句，如果模块在当前的搜索路径就会被导入。
from 语句让你从模块中导入一个指定的部分到当前命名空间中
from modname import name1[, name2[, ... nameN]]

当你导入一个模块，Python 解析器对模块位置的搜索顺序是：
1、当前目录
2、如果不在当前目录，Python 则搜索在 shell 变量 PYTHONPATH 下的每个目录。
3、如果都找不到，Python会察看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/。

命名空间：
命名空间是一个包含了变量名称们（键）和它们各自相应的对象们（值）的字典。是一个字典
Python 会智能地猜测一个变量是局部的还是全局的，它假设任何在函数内赋值的变量都是局部的。因此，如果要给函数内的全局变量赋值，必须使用 global 语句。

dir()函数返回一个排好序的字符串列表，内容是一个模块里定义过的名字

python中的包：包就是文件夹，但该文件夹下必须存在 __init__.py 文件, 该文件的内容可以为空。__int__.py用于标识当前文件夹是一个包。