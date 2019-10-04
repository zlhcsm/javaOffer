# Redis之事务机制
### 事务机制
`Redis`通过`MULTI`、`EXEC`、`WATCH`命令来实现事务，Reids在事务执行期间，服务器不会中断事务去执行其他命令，而是在事务的所有命令执行完毕后再执行其他命令，因为Redis是单线程处理模型。以下是一个事务执行流程：
```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name luo
QUEUED
127.0.0.1:6379> set age 26
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
```
*一个事务从开始到结束通常会经历以下3个阶段：*

- 事务开始：multi命令标志着事务的开始；
- 命令入列：当事务开始后，所有命令都不会立即执行，而是将命令放入一个事务队列中；
- 事务执行：exec命令将开始执行事务，服务器会遍历这个事务队列，执行队列中所有的命令，最后将执行后的结果都返回给该客户端。注意，该事务队列是FIFO的。

### WATCH命令的实现
WATCH是一个乐观锁，它可以在MULTI命令执行前，监视任意多个key，并在EXEC执行前，如果有一个或多个被监视key被更新，则拒绝执行事务，并向客户端返回执行失败的空回复。以下是一个事务执行失败的例子（在EXEC前另一个客户端更改了name的值）：
```
127.0.0.1:6379> watch name
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name luoxn28
QUEUED
127.0.0.1:6379> exec
(nil)
```
每个Reids数据库都保存着一个watched_keys字典，这个字典的键是某个被WATCH命令监控的数据库键，而字典的值是一个链表，链表记录了所有监控对应数据库键的客户端：
```
typedef struct redisDb {
 dict *dict; /* The keyspace for this DB */
 dict *expires; /* Timeout of keys with a timeout set */
 dict *blocking_keys; /* Keys with clients waiting for data (BLPOP)*/
 dict *ready_keys; /* Blocked keys that received a PUSH */
 dict *watched_keys; /* WATCHED keys for MULTI/EXEC CAS */
 int id; /* Database ID */
 long long avg_ttl; /* Average TTL, just for stats */
 list *defrag_later; /* List of key names to attempt to defrag one by one, gradually. */
} redisDb;
```
通过watched_keys字典可以很容易的清楚哪些数据库键被监视，以及哪些客户端正在监视这些数据库键。那么监控机制什么时候被触发的呢？所有对数据库键进行修改的命令，比如set/lpush/sadd/zrem/del/flushdb等，在执行之后都会对watched_keys字典进行检查，检查是否有刚才修改的数据库键，如果有的话则会将监控被修改键对应链表上所有客户端的REDIS_DIRTY_CAS标志打开，表示该客户端事务安全性被破坏。

在收到EXEC命令时，Redis会根据客户端对应连接是否打开了REDIS_DIRTY_CAS标志来决定是否执行事务。如果被打开，则拒绝执行事务，因为此时事务提交已不再安全；否则将执行事务
### Redis事务与MySQL事务比较
传统数据库系统中常用ACID属性来检验事务的安全性和可靠性，关于MySQL资料较多，这里不再赘述。
- 原子性：Reids事务不具有回滚机制，即使事务中有部分命令执行错误，事务也会继续执行之后的命令直到结束，并且之前执行的命令不受任何影响。Redis为什么不支持回滚机制呢，其作者解释道，不支持事务回滚是因为这种复杂的功能和Reids追求的简单高效设计主旨不符，并且他认为，Reids事务的执行中的错误通常是由程序错误导致的，这种错误在实际生产环境中较少出现，因此没必要为Reids开发事务回滚功能。
- 一致性：一致性指数据库在执行事务之前是一致的，那么在事务的执行之后，无论其成功与否，数据库都仍然是一致的。Redis通过以下几个机制来保证事务的一致性：
- 入队错误检查：命令入事务队列时，会检查该命令，如果该命令不存在或者格式不正确，那么Reids拒绝执行该事务。
- 事务在执行过程中发生了错误，比如对数据库键进行了错误类型操作，此时Redis不会中断事务的执行，而是继续执行后面的命令，并且已执行的命令不会受到错误命令的影响。
- Reids运行在某一持久化机制下，事务执行过程中发生停机都不会影响数据库的一致性，因为可以通过持久化文件来恢复出数据库之前保存的状态。
- 隔离性：隔离性是指在事务并发执行时，各个事务之间不会互相影响。因为Reids是单线程模型，所以其不存在事务并发执行问题，都是以串行方式执行事务的，可以看成是“串行化隔离级别”。
- 持久性：事务的持久性是指当事务执行完毕后，其数据已经持久化到磁盘（永久介质）上了，即使服务器停机，事务执行的结果也不会丢失。Reids事务不过是简单的多条命令的集合，并没有为其提供额外的持久化机制，因为事务的持久化依赖于Redis所使用的持久化机制。如果Redis没有配置AOF或者RDB，则Redis事务可以说是没有持久性的。