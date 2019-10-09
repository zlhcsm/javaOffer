# 华为面经A01
> 这是一个悲伤的面试...
## 一面
#### 1，float中32bit中哪些代表符号位、小数位、整数位、指数位？
*float：*  1bit（符号位） 8bits（指数位） 23bits（尾数位）  
[知识拓展：float与double的范围和精度]()
#### 2，servelt生命周期的初始化方法init是什么时候进行的？
Servlet 创建于用户第一次调用对应于该 Servlet 的 `URL` 时，但是您也可以指定 `Servlet` 在服务器第一次启动时被加载。  
[知识扩展：Servlet 生命周期](https://www.runoob.com/servlet/servlet-life-cycle.htmlh)
#### 3，http和https中的ssl怎么实现的？
Secure Sockets Laye（安全套接层）标准化之后的名称改为 TLS（是“Transport Layer Security”的缩写），中文叫做“传输层安全协议”。  

HTTPS 其实是“HTTP over SSL”或“HTTP over TLS”，它是 HTTP 与 SSL/TSL 的结合使用而已。
- 客户端向服务器端索要并验证公钥。
- 双方协商生成"对话密钥"。
- 双方采用"对话密钥"进行加密通信。 
上面过程的前两步，又称为"握手阶段"（handshake）  
[知识扩展：一篇文章看明白 HTTP，HTTPS，SSL/TSL 之间的关系](https://blog.csdn.net/freekiteyu/article/details/76423436)
[阮一峰这个好](https://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)
#### 4，代理模式中的Cglib代理怎么实现的？把原理说一下
>小课堂：cglib与动态代理最大的区别就是(Code Generation Library)
- 使用动态代理的对象必须实现一个或多个接口
- 使用cglib代理的对象则无需实现接口，达到代理类无侵入。

```java

public class Client {
    public static void main(String[] args) {
            // 代理类class文件存入本地磁盘方便我们反编译查看源码
            System.setProperty(DebuggingClassWriter.DEBUG_LOCATION_PROPERTY, "D:\\code");
            // 通过CGLIB动态代理获取代理对象的过程,创建一个enhancer对象
            Enhancer enhancer = new Enhancer();
            // 设置enhancer对象的父类
            enhancer.setSuperclass(HelloService.class);
            // 设置enhancer的回调对象
            enhancer.setCallback(new MyMethodInterceptor());
            // 创建代理对象
            HelloService proxy= (HelloService)enhancer.create();
            // 通过代理对象调用目标方法
            proxy.sayHello();
        }
}
```

[👍知识拓展：Java三种代理模式：静态代理、动态代理和cglib代理](https://segmentfault.com/a/1190000011291179)

---
`拓展知识`
#### float与double的范围和精度
类型 | 符号位 | 指数位 | 尾数位 | 总共
---|---|---|---|---
float | 1 (31位) | 8 (23-30) | 23 (0-22) | 32(4个字节)
double | 1 (63位) | 11 (65-52) | 52 (0-51) | 64(8个字节)
 

1，取值`范围`主要看指数部分：   
- float的指数部分有8bit(2^8)，由于是有符号型，所以得到对应的指数范围-128~127。 
- double的指数部分有11bit(2^11)，由于是有符号型，所以得到对应的指数范围-1024~1023。

由于float的指数部分对应的指数范围为-128~127，所以取值范围为：   
-2^128到2^127，约等于-3.4E38 — +3.4E38   

2，`精度`主要看尾数的位数。
浮点数在内存中是按科学计数法来存储的，其整数部分始终是一个隐含着的“1”，由于它是不变的，故不能对精度造成影响。
- float：2^23 = 8388608，一共七位，这意味着最多能有7位有效数字，但绝对能保证的为6位，也即float的精度为6~7位有效数字；
- double：2^52 = 4503599627370496，一共16位，同理，double的精度为15~16位。
#### 