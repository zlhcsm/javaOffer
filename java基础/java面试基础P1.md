1，java标识符规则
- 标识以数字,字符,下划线,以及美元$符组成.（不能包括@、%、空格等）
- 不能以数字开头. 
- 不能与JAVA关键字重复
-  严格区分的大小写,（Flag和flag是两个变量）

2，Servlet生命周期分为三个阶段：
- 初始化阶段，调用`init()`方法
- 响应客户请求阶段，调用`service()`方法
- 终止阶段，调用`destroy()`方法

3，Java中的四类八种基本数据类型
第一类：整数类型  `byte short int long`
第二类：浮点型  `float double`
第三类：逻辑型    `boolean`(它只有两个值可取true false)
第四类：字符型  `char`

4，int  类型和  Integer  类的转换
包装类就是引用类型，基本数据类型就是值类型。

5，一个.java文件中，只能存在一个类是用public修饰的，并且这个类必须与类名一致，文件中其他的类不能是public权限的，但可以有很多个类。

6，redirect 和 forward
`redirect：`请求重定向：客户端行为
`forward：`请求转发:服务器行为，地址栏不变

7，异常处理
- 编译时异常必须显示处理，运行时异常交给虚拟机。
- 运行时异常可以不处理。当出现这样的异常时，总是由虚拟机接管。
- 出现运行时异常后，系统会把异常一直往上层抛，一直遇到处理代码。
- 如果不对运行时异常进行处理，那么出现运行时异常之后，要么是线程中止，要么是主程序终止。

8，==  优先级高于 三目运算

9，`getMethods() `和`getDeclaredMethods()`
- getMethods()返回某个类的所有公用（public）方法包括其继承类的公用方法，包括它所实现接口的方法。
- getDeclaredMethods()对象表示的类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。包括它所实现接口的方法。

10，Number类可以被继承，Integer，Float，Double等都继承自Number类

11，JSP中的exception
exception是JSP九大内置对象之一，其实例代表其他页面的异常和错误。只有当页面是错误处理页面时，即isErroePage为 true时，该对象才可以使用。对于C项，errorPage的实质就是JSP的异常处理机制,发生异常时才会跳转到 errorPage指定的页面，没必要给errorPage再设置一个errorPage。所以当errorPage属性存在时， isErrorPage属性值为false

12，`super`和`this`
super和this都只能位于构造器的第一行，而且不能同时使用，这是因为会造成初始化两次，this用于调用重载的构造器，super用于调用父类被子类重写的方法

13，修饰符

           作用域       当前类    同一package    子孙类        其他
           
           public        √         √             √           √ 

          protected     √          √             √           × 

          friendly      √          √             ×           × 

          private       √          ×             ×           ×
          
14，方法的重写（override）两同两小一大原则：
- 方法名相同，参数类型相同
- 子类返回类型小于等于父类方法返回类型，
- 子类抛出异常小于等于父类方法抛出异常，
- 子类访问权限大于等于父类方法访问权限。

15，Java表达式转型规则由低到高转换：
- 所有的byte,short,char型的值将被提升为int型；
- 如果有一个操作数是long型，计算结果是long型；
- 如果有一个操作数是float型，计算结果是float型；
- 如果有一个操作数是double型，计算结果是double型；
- 被fianl修饰的变量不会自动改变类型，当2个final修饰相操作时，结果会根据左边变量的类型而转化。

16，jdbc statement的描述
- Statement、PreparedStatement和CallableStatement都是接口(interface)。 
- Statement继承自Wrapper、PreparedStatement继承自Statement、CallableStatement继承自PreparedStatement。 
- Statement接口提供了执行语句和获取结果的基本方法； 
PreparedStatement接口添加了处理 IN 参数的方法； 
CallableStatement接口添加了处理 OUT 参数的方法。  
- `*Statement:*` 
普通的不带参的查询SQL；支持批量更新,批量删除; 
`*PreparedStatement:*`
可变参数的SQL,编译一次,执行多次,效率高; 
安全性好，有效防止Sql注入等问题; 
支持批量更新,批量删除; 
`*CallableStatement:*`
继承自PreparedStatement,支持带参数的SQL操作; 支持调用存储过程,提供了对输出和输入/输出参数(INOUT)的支持; 
- Statement每次执行sql语句，数据库都要执行sql语句的编译 ， 
最好用于仅执行一次查询并返回结果的情形，效率高于PreparedStatement。 
- PreparedStatement是预编译的，使用PreparedStatement有几个好处 
1. 在执行可变参数的一条SQL时，PreparedStatement比Statement的效率高，因为DBMS预编译一条SQL当然会比多次编译一条SQL的效率要高。 
2. 安全性好，有效防止Sql注入等问题。 
3.  对于多次重复执行的语句，使用PreparedStament效率会更高一点，并且在这种情况下也比较适合使用batch； 
4.  代码的可读性和可维护性。

17，线程的阻塞情况
要想答对这道题，要区分“终止” 和 “阻塞”：
终止：这个线程不会在进入“就绪态”，宣告死亡，即“死亡状态”。
阻塞：进入阻塞态的线程还可以再进入“就绪态”，等待下一次 CPU 时间。

然后是对线程的 5 个状态的理解：
1. 新建，刚刚新建的线程，还未进入就绪队列
2. 就绪，进入就绪队列的线程拥有了获得 CPU 时间的机会，但不是一定会马上执行，与线程调度有关。
3. 运行，获得了 CPU 时间，正在被执行的线程。
4. 阻塞，进入阻塞状态的线程只是暂时失去了 CPU 时间，该类线程没有结束，“阻塞态”的线程只能进入到“就绪态”。
5. 死亡，死亡的线程即彻底结束了。

下面总结下使一个线程进入阻塞状态的方法：
1. sleep() / suspend()
2. 发生IO阻塞
3. 等待同步锁
4. 等待通知

解除一个线程的阻塞状态，使之进入就绪态的方法：
1. sleep() 指定的睡眠时间到了
2. IO阻塞解除
3. 获得同步锁
4. 收到通知
5. 调用了 suspend() 的线程阻塞后，再调用 resume() 解除阻塞

18，类加载顺序
①父类静态变量和静态代码块(按照声明顺序)；
②子类静态变量和静态代码块(按照声明顺序)；
③父类成员变量和代码块(按照声明顺序)；
④父类构造器；
⑤子类成员变量和代码块(按照声明顺序)；
⑥子类构造器。 

19，异常分类
Java语言中的异常处理包括`声明异常`、`抛出异常`、`捕获异常`和`处理异常`四个环节。
- throw用于抛出异常。
- throws关键字可以在方法上声明该方法要抛出的异常，然后在方法内部通过throw抛出异常对象。
- try是用于检测被包住的语句块是否出现异常，如果有异常，则抛出异常，并执行catch语句。
- cacth用于捕获从try中抛出的异常并作出处理。
- finally语句块是不管有没有出现异常都要执行的内容。

20，反射
反射指的是在运行时能够分析类的能力的程序。

反射机制可以用来：
1.在运行时分析类的能力--检查类的结构--所用到的就是java.lang.reflect包中的Field、Method、Constructor，分别用于描述类的与、方法和构造器。A中的Class类在java.lang中。
2.在运行时查看对象。
3.实现通用的数组操作代码。

反射机制的功能：
在运行时判断任意一个对象所属的类；在运行时构造任意一个类的对象；在运行时判断任意一个类所具有的成员变量和方法；在运行时调用任意一个对象的方法；生成动态代理。

反射机制常见作用：
动态加载类、动态获取类的信息（属性、方法、构造器）；动态构造对象；动态调用类和对象的任意方法、构造器；动态调用和处理属性；获取泛型信息（新增类型：ParameterizedType,GenericArrayType等）；处理注解（反射API:getAnnotationsdeng等）。

反射机制性能问题：
反射会降低效率。
void setAccessible(boolean flag):是否启用访问安全检查的开关，true屏蔽Java语言的访问检查，使得对象的私有属性也可以被查询和设置。禁止安全检查，可以提高反射的运行速度。
可以考虑使用：cglib/javaassist操作。
