# 华为面经A07
## 一面
#### 1，反射存在的意义
#### 2，反射通过什么完成
#### 3，JVM内存模型
#### 4，动态常量池/经态常量池
#### String
#### OSI七层结构
#### URL访问过程
#### tcp握手和分手
#### 浅拷贝和深拷贝
#### equal和hashcode
#### hashmap/hashtable 为什么不可以（可以）为null
1，从源码分析  
- HashMap在put的时候会调用hash()方法来计算key的hashcode值，可以从hash算法中看出当key==null时返回的值为0。因此key为null时，hash算法返回值为0，不会调用key的hashcode方法
- 上面可以看出当Hashtable存入的value为null时，抛出NullPointerException异常。如果value不为null，而key为空，在执行到int  hash = key.hashCode()时同样会抛出NullPointerException异常
2，从设计师角度看  
也许Hashtable类的设计者当时认为null作为key 和value 是没有什么用的。
#### 会那些设计模式
#### 撕代码，最长严格增长子序列