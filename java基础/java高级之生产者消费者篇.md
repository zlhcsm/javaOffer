# 生产者消费者实现
[参考自掘金用户：](https://juejin.im/entry/596343686fb9a06bbd6f888c)  
[参考自：猴子007](https://monkeysayhi.github.io/2017/10/08/Java%E5%AE%9E%E7%8E%B0%E7%94%9F%E4%BA%A7%E8%80%85-%E6%B6%88%E8%B4%B9%E8%80%85%E6%A8%A1%E5%9E%8B/)
### 生产者消费者模型
网上有很多生产者-消费者模型的定义和实现。本文研究最常用的有界生产者-消费者模型，简单概括如下：

- 生产者持续生产，直到缓冲区满，阻塞；缓冲区不满后，继续生产
- 消费者持续消费，直到缓冲区空，阻塞；缓冲区不空后，继续消费
- 生产者可以有多个，消费者也可以有多个
![](https://user-gold-cdn.xitu.io/2017/7/10/115bd419948f2b87a017fb7504d1b0ab?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
可通过如下条件验证模型实现的正确性：
- 同一产品的消费行为一定发生在生产行为之后
- 任意时刻，缓冲区大小不小于0，不大于限制容量
该模型的应用和变种非常多，不赘述。
### 几种写法
#### 准备
> 面试时可语言说明以下准备代码。关键部分需要实现，如AbstractConsumer，

下面会涉及多种生产者-消费者模型的实现，可以先抽象出关键的接口，并实现一些抽象类：
`消费者`
```java
public interface Consumer {
  void consume() throws InterruptedException;
}
```
`生产者`
```java
public interface Producer {
  void produce() throws InterruptedException;
}
```
`实现消费者抽象类`
```java
abstract class AbstractConsumer implements Consumer, Runnable {
  @Override
  public void run() {
    // 消费者不断的空转
    while (true) {
      try {
        consume();
      } catch (InterruptedException e) {
        e.printStackTrace();
        break;
      }
    }
  }
}
```
`实现生产者抽象类`
```java
abstract class AbstractProducer implements Producer, Runnable {
  @Override
  public void run() {
    while (true) {
      try {
        produce();
      } catch (InterruptedException e) {
        e.printStackTrace();
        break;
      }
    }
  }
}
```
不同的模型实现中，生产者、消费者的具体实现也不同，所以需要为模型定义抽象工厂方法
```java
public interface Model {
  Runnable newRunnableConsumer();
  Runnable newRunnableProducer();
}
```
我们将Task作为生产和消费的单位：
```java
public class Task {
  public int no;
  public Task(int no) {
    this.no = no;
  }
}
```
> 如果需求还不明确（这符合大部分工程工作的实际情况），建议边实现边抽象，不要“面向未来编程”。

#### 实现一：BlockingQueue

BlockingQueue的写法最简单。核心思想是，`把并发和容量控制封装在缓冲区中`。而BlockingQueue的性质天生满足这个要求。  
BlockingQueue即阻塞队列，从阻塞这个词可以看出，在某些情况下对阻塞队列的访问可能会造成阻塞。被阻塞的情况主要有如下两种:  
1，当队列满了的时候进行入队列操作  
2，当队列空了的时候进行出队操作  

操作 | 抛异常 | 特定值 | 阻塞 | 超时
---|---|---|---|---
插入 | add(o) | offer(o) | put(o) | offer(o, timeout, timeunit)
移除 | remove(o) | poll(o) | take(o) | 

```java
public class BlockingQueueModel implements Model {
  private final BlockingQueue<Task> queue;
  private final AtomicInteger increTaskNo = new AtomicInteger(0);
  public BlockingQueueModel(int cap) {
    // LinkedBlockingQueue 的队列不 init，入队时检查容量；ArrayBlockingQueue 在创建时 init
    this.queue = new LinkedBlockingQueue<>(cap);
  }
  @Override
  public Runnable newRunnableConsumer() {
    return new ConsumerImpl();
  }
  @Override
  public Runnable newRunnableProducer() {
    return new ProducerImpl();
  }
  private class ConsumerImpl extends AbstractConsumer implements Consumer, Runnable {
    @Override
    public void consume() throws InterruptedException {
      Task task = queue.take();
      // 固定时间范围的消费，模拟相对稳定的服务器处理过程
      Thread.sleep(500 + (long) (Math.random() * 500));
      System.out.println("consume: " + task.no);
    }
  }
  private class ProducerImpl extends AbstractProducer implements Producer, Runnable {
    @Override
    public void produce() throws InterruptedException {
      // 不定期生产，模拟随机的用户请求
      Thread.sleep((long) (Math.random() * 1000));
      Task task = new Task(increTaskNo.getAndIncrement());
      System.out.println("produce: " + task.no);
      queue.put(task);
    }
  }
  public static void main(String[] args) {
    Model model = new BlockingQueueModel(3);
    for (int i = 0; i < 2; i++) {
      new Thread(model.newRunnableConsumer()).start();
    }
    for (int i = 0; i < 5; i++) {
      new Thread(model.newRunnableProducer()).start();
    }
  }
}
```
截取前面的一部分输出：
```
roduce: 0
produce: 4
produce: 2
produce: 3
produce: 5
consume: 0
produce: 1
consume: 4
produce: 7
consume: 2
produce: 8
consume: 3
produce: 6
consume: 5
produce: 9
consume: 1
produce: 10
consume: 7
```
由于操作“出队/入队+日志输出”不是原子的，所以上述日志的绝对顺序与实际的出队/入队顺序有出入，
但对于同一个任务号task.no，其consume日志一定出现在其produce日志之后，
即：同一任务的消费行为一定发生在生产行为之后。缓冲区的容量留给读者验证。符合两个验证条件。
#### 实现二：wait && notify
如果不能将并发与容量控制都封装在缓冲区中，就只能由消费者与生产者完成。最简单的方案是使用朴素的wait && notify机制。
```java
public class WaitNotifyModel implements Model {
  private final Object BUFFER_LOCK = new Object();
  private final Queue<Task> buffer = new LinkedList<>();
  private final int cap;
  private final AtomicInteger increTaskNo = new AtomicInteger(0);
  public WaitNotifyModel(int cap) {
    this.cap = cap;
  }
  @Override
  public Runnable newRunnableConsumer() {
    return new ConsumerImpl();
  }
  @Override
  public Runnable newRunnableProducer() {
    return new ProducerImpl();
  }
  private class ConsumerImpl extends AbstractConsumer implements Consumer, Runnable {
    @Override
    public void consume() throws InterruptedException {
      synchronized (BUFFER_LOCK) {
        while (buffer.size() == 0) {
          BUFFER_LOCK.wait();
        }
        Task task = buffer.poll();
        assert task != null;
        // 固定时间范围的消费，模拟相对稳定的服务器处理过程
        Thread.sleep(500 + (long) (Math.random() * 500));
        System.out.println("consume: " + task.no);
        BUFFER_LOCK.notifyAll();
      }
    }
  }
  private class ProducerImpl extends AbstractProducer implements Producer, Runnable {
    @Override
    public void produce() throws InterruptedException {
      // 不定期生产，模拟随机的用户请求
      Thread.sleep((long) (Math.random() * 1000));
      synchronized (BUFFER_LOCK) {
        while (buffer.size() == cap) {
          BUFFER_LOCK.wait();
        }
        Task task = new Task(increTaskNo.getAndIncrement());
        buffer.offer(task);
        System.out.println("produce: " + task.no);
        BUFFER_LOCK.notifyAll();
      }
    }
  }
  public static void main(String[] args) {
    Model model = new WaitNotifyModel(3);
    for (int i = 0; i < 2; i++) {
      new Thread(model.newRunnableConsumer()).start();
    }
    for (int i = 0; i < 5; i++) {
      new Thread(model.newRunnableProducer()).start();
    }
  }
}
```
朴素的wait && notify机制不那么灵活，但足够简单。
#### 实现三：简单的Lock && Condition
我们要保证理解wait && notify机制。实现时可以使用Object类提供的wait()方法与notifyAll()方法，但更推荐的方式是使用java.util.concurrent包提供的Lock && Condition。
```java
	
public class LockConditionModel1 implements Model {
  private final Lock BUFFER_LOCK = new ReentrantLock();
  private final Condition BUFFER_COND = BUFFER_LOCK.newCondition();
  private final Queue<Task> buffer = new LinkedList<>();
  private final int cap;
  private final AtomicInteger increTaskNo = new AtomicInteger(0);
  public LockConditionModel1(int cap) {
    this.cap = cap;
  }
  @Override
  public Runnable newRunnableConsumer() {
    return new ConsumerImpl();
  }
  @Override
  public Runnable newRunnableProducer() {
    return new ProducerImpl();
  }
  private class ConsumerImpl extends AbstractConsumer implements Consumer, Runnable {
    @Override
    public void consume() throws InterruptedException {
      BUFFER_LOCK.lockInterruptibly();
      try {
        while (buffer.size() == 0) {
          BUFFER_COND.await();
        }
        Task task = buffer.poll();
        assert task != null;
        // 固定时间范围的消费，模拟相对稳定的服务器处理过程
        Thread.sleep(500 + (long) (Math.random() * 500));
        System.out.println("consume: " + task.no);
        BUFFER_COND.signalAll();
      } finally {
        BUFFER_LOCK.unlock();
      }
    }
  }
  private class ProducerImpl extends AbstractProducer implements Producer, Runnable {
    @Override
    public void produce() throws InterruptedException {
      // 不定期生产，模拟随机的用户请求
      Thread.sleep((long) (Math.random() * 1000));
      BUFFER_LOCK.lockInterruptibly();
      try {
        while (buffer.size() == cap) {
          BUFFER_COND.await();
        }
        Task task = new Task(increTaskNo.getAndIncrement());
        buffer.offer(task);
        System.out.println("produce: " + task.no);
        BUFFER_COND.signalAll();
      } finally {
        BUFFER_LOCK.unlock();
      }
    }
  }
  public static void main(String[] args) {
    Model model = new LockConditionModel1(3);
    for (int i = 0; i < 2; i++) {
      new Thread(model.newRunnableConsumer()).start();
    }
    for (int i = 0; i < 5; i++) {
      new Thread(model.newRunnableProducer()).start();
    }
  }
}
```
#### 实现四：更高并发性能的Lock && Condition
现在，如果做一些实验，你会发现，实现一的并发性能高于实现二、三。暂且不关心BlockingQueue的具体实现，来分析看如何优化实现三（与实现二的思路相同，性能相当）的性能。
```java
	
public class LockConditionModel2 implements Model {
  private final Lock CONSUME_LOCK = new ReentrantLock();
  private final Condition NOT_EMPTY = CONSUME_LOCK.newCondition();
  private final Lock PRODUCE_LOCK = new ReentrantLock();
  private final Condition NOT_FULL = PRODUCE_LOCK.newCondition();
  private final Buffer<Task> buffer = new Buffer<>();
  private AtomicInteger bufLen = new AtomicInteger(0);
  private final int cap;
  private final AtomicInteger increTaskNo = new AtomicInteger(0);
  public LockConditionModel2(int cap) {
    this.cap = cap;
  }
  @Override
  public Runnable newRunnableConsumer() {
    return new ConsumerImpl();
  }
  @Override
  public Runnable newRunnableProducer() {
    return new ProducerImpl();
  }
  private class ConsumerImpl extends AbstractConsumer implements Consumer, Runnable {
    @Override
    public void consume() throws InterruptedException {
      int newBufSize = -1;
      CONSUME_LOCK.lockInterruptibly();
      try {
        while (bufLen.get() == 0) {
          System.out.println("buffer is empty...");
          NOT_EMPTY.await();
        }
        Task task = buffer.poll();
        newBufSize = bufLen.decrementAndGet();
        if (newBufSize > 0) {
          NOT_EMPTY.signalAll();
        }
      } finally {
        CONSUME_LOCK.unlock();
      }
      if (newBufSize < cap) {
        PRODUCE_LOCK.lockInterruptibly();
        try {
          NOT_FULL.signalAll();
        } finally {
          PRODUCE_LOCK.unlock();
        }
      }
      
      assert task != null;
      // 固定时间范围的消费，模拟相对稳定的服务器处理过程
      Thread.sleep(500 + (long) (Math.random() * 500));
      System.out.println("consume: " + task.no);
    }
  }
  private class ProducerImpl extends AbstractProducer implements Producer, Runnable {
    @Override
    public void produce() throws InterruptedException {
      // 不定期生产，模拟随机的用户请求
      Thread.sleep((long) (Math.random() * 1000));
      int newBufSize = -1;
      PRODUCE_LOCK.lockInterruptibly();
      try {
        while (bufLen.get() == cap) {
          System.out.println("buffer is full...");
          NOT_FULL.await();
        }
        Task task = new Task(increTaskNo.getAndIncrement());
        buffer.offer(task);
        newBufSize = bufLen.incrementAndGet();
        System.out.println("produce: " + task.no);
        if (newBufSize < cap) {
          NOT_FULL.signalAll();
        }
      } finally {
        PRODUCE_LOCK.unlock();
      }
      if (newBufSize > 0) {
        CONSUME_LOCK.lockInterruptibly();
        try {
          NOT_EMPTY.signalAll();
        } finally {
          CONSUME_LOCK.unlock();
        }
      }
    }
  }
  private static class Buffer<E> {
    private Node head;
    private Node tail;
    Buffer() {
      // dummy node
      head = tail = new Node(null);
    }
    public void offer(E e) {
      tail.next = new Node(e);
      tail = tail.next;
    }
    public E poll() {
      head = head.next;
      E e = head.item;
      head.item = null;
      return e;
    }
    private class Node {
      E item;
      Node next;
      Node(E item) {
        this.item = item;
      }
    }
  }
  public static void main(String[] args) {
    Model model = new LockConditionModel2(3);
    for (int i = 0; i < 2; i++) {
      new Thread(model.newRunnableConsumer()).start();
    }
    for (int i = 0; i < 5; i++) {
      new Thread(model.newRunnableProducer()).start();
    }
  }
}
```
### 总结
实现一必须掌握，实现四至少要清楚表述；实现二、三掌握一个就好。