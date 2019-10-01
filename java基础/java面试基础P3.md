- [类加载过程](#1)
- [Statement()](#2)
- [Exception（异常）](#3)
- [JVM设置参数初始化](#4)
- [正则表达式](#5)
##### 类加载过程<span id = 1></span>
>整个生命周期包括：`加载`（Loading）、`验证`（Verification）、`准备`(Preparation)、`解析`(Resolution)、`初始化`(Initialization)、`使用`(Using)和`卸载`(Unloading)7个阶段。
>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191001090401709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6enpsZWkxMjMxMjMxMjM=,size_16,color_FFFFFF,t_70) [详情查看](https://www.nowcoder.com/test/question/done?tid=28220199&qid=26108#summary)
`加载阶段`
虚拟机要完成一下三件事情：
1，通过一个类的全限定名来获取定义此类的二进制字节流  
2，将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构
3，在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据的访问入口
`验证阶段`
**目的：** 确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。
**检验项目：** 文件格式验证、元数据验证、字节码验证、符号引用验证
`准备`
准备阶段是正式为`类变量`分配`内存`并设置==类变量初始值==的阶段，这些变量所使用的内存都将在==方法区==中进行分配。
注意：“通常情况”下是数据类型的零值！“特殊情况”当被final修饰时，会在准备阶段初始化为指定的值。
`解析`
解析阶段是虚拟机将常量池内的符号引用替换为直接引用的过程
`初始化`
到了初始化阶段，才真正开始执行类中定义的java程序代码。
##### Statement()<span id = 2></span>
>1、`Statement`对象用于执行`不带参数`的简单`SQL语句`。 
2、Prepared Statement 对象用于执行`预编译SQL`语句。 
3、Callable Statement对象用于执行对`存储过程`的调用
##### Exception（异常）<span id = 3></span>
>主要包含RuntimeException等`运行时异常`和IOException，SQLException等`非运行时异常`。
<br>运行时异常 包括：都是`RuntimeException类及其子类异常`，如`NullPointerException`(空指针异常)、`IndexOutOfBoundsException`(下标越界异常)等，这些异常是不检查异常，==程序中可以选择捕获处理，也可以不处理==。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。<br>
运行时异常的特点是Java编译器不会检查它，也就是说，当程序中可能出现这类异常，即使没有用try-catch语句捕获它，也没有用throws子句声明抛出它，也会编译通过。<br>
非运行时异常（编译异常） 包括：`RuntimeException以外的异常`，类型上都属于Exception类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常，一般情况下不自定义检查异常
##### JVM设置参数初始化<span id = 4></span>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191001130132807.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6enpsZWkxMjMxMjMxMjM=,size_16,color_FFFFFF,t_70)
##### 正则表达式<span id = 5></span>
1.什么是正则表达式的贪婪与非贪婪匹配
如：
```java
String str="abcaxc";

Patter p="ab*c";
```
`贪婪匹配`:正则表达式一般趋向于`最大`长度匹配，也就是所谓的贪婪匹配。如上面使用模式p匹配字符串str，结果就是匹配到：abcaxc(ab*c)。

`非贪婪匹配`：就是匹配到结果就好，就`少`的匹配字符。如上面使用模式p匹配字符串str，结果就是匹配到：abc(ab*c)。

2.编程中如何区分两种模式
默认是贪婪模式；在量词后面直接加上一个问号？就是非贪婪模式。
- 量词：{m,n}：m到n个
-  *：任意多个
- +：一个到多个
- ？：0或一个
