# 华为面试A06
## 一面
#### 1，简单说说面向对象的特征以及六大原则
[参考资料](https://blog.csdn.net/u014590757/article/details/79815831)  
面向对象的三大特性是"封装、"多态"、"继承"  
- 封装：客观事物封装成抽象的类
- 继承：某个类型的对象获得另一个类型的对象的属性和方法
- 多态：一个类实例的相同方法在不同情形有不同表现形式   
五大原则是"单一职责原则"、"开放封闭原则"、"里氏替换原则"、"依赖倒置原则"、"接口分离原则"、"迪米特原则（高内聚低耦合）"
- 单一职责原则：一个类的功能要单一，不能包罗万象
- 开放封闭原则：扩展开放，更改封闭
- 里式替换原则：子类应当可以替换父类并出现在父类能够出现的任何地方
- 依赖倒置原则：面向接口编程
- 接口分离原则：接口要小而专，决不能大而全。
- 迪米特法则：最少知识原则
#### 2，谈谈final、finally、finalize的区别
- final用于声明属性，方法和类，分别表示属性不可变，方法不可覆盖，类不可继承
- finally是异常处理语句结构的一部分，表示总要执行
- finalize是Object类的一个方法啊，在垃圾收集器执行的时候会调用该回收对象的此方法，可以覆盖此方法提供垃圾收集时的其他资源回收；例如关闭文件等
#### 3，Java中==、equals与hashCode的区别和联系
#####`hashcode() 和 equals()方法有什么联系`
① 相等（相同）的对象必须具有相等的哈希码  
② 如果两个对象的hashcode()相同，它们并不一定相同
##### `== 和 euqals()的关系`
① "=="对比两个对象的内存引用。如果两边是基本类型，就是比较数值是否相等。    
② equals()在底层也是实现了"=="方法。但是String类重写了equals方法，比较的是值是否相等。
##### `Object若不重写hashCode()的话，hashCode()如何计算出来`
Object的hashCode方法是本地方法，也就是用"C语言"或c++实现的，该方法直接返回对象的内存地址
##### `HashSet为什么重写equals还要重写hashcode方法`
- 重写hashcode是为了对同一个key，能得到相同的hashcode
- 重写是为了向hashmap表名当前对象和key上所保存的对象是相等的。
#### 4，谈谈Java容器ArrayList、LinkedList、HashMap、HashSet的理解，以及应用场景
##### Array和ArrayList有什么区别
- Array 可以包含基本类型和对象类型，ArrayList 只能包含对象类型。
- Array 大小是固定的，ArrayList 的大小是动态变化的。
- ArrayList 提供了更多的方法和特性，比如：addAll()，removeAll()，iterator() 等等。 
对于基本类型数据，集合使用自动装箱来减少编码工作量。但是，当处理固定大小的基本数据类型的时候，这种方式相对比较慢。 因此，针对于基本数据类型，使用Array。
##### ArrayList和LinkedList有什么区别
- LinkedList 实现了 List 和 Deque 接口，一般称为双向链表；ArrayList 实现了 List 接口，动态数组；
- LinkedList 在插入和删除数据时效率更高，ArrayList 在查找某个 index 的数据时效率更高；
- LinkedList 比 ArrayList 需要更多的内存；
##### HashMap和HashSet有什么区别
- hashMap存储的是key-val键值对，而hashset存储的是对象
- hashMao实现了map接口，HashSet实现了Set接口
- HashMap使用put方法加入元素，HashSet使用add()方法加入值
- HashMap使用的键值对来计算Hash值，HashSet使用的是key来计算
- HashMap比较快，HashSet比较慢

##### HashMap和HasmTable有什么区别
- HashMap没有考虑同步，是线程不安全的；Hashtable使用了synchronized关键字，是线程安全的；
- HashMap允许K/V都为null；后者K/V都不允许为null；
#### 5，谈谈线程的基本状态，其中的wait() sleep()  yield()方法的区别。
## 二面
- 1，JVM性能调优的监控工具了解那些？
- 2，简单谈谈JVM内存模型，以及volatile关键字
- 3，垃圾收集器与内存分配策略
- 4，垃圾收集算法
- 5，MySQL几种常用的存储引擎区别
- 6，数据库的隔离级别
- 7,手撸算法：5亿整数怎么排