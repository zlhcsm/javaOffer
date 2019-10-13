[参考知乎文章：Java架构](https://zhuanlan.zhihu.com/p/74865197)  
[参考思否用户：Object](https://segmentfault.com/a/1190000010293791)

- 目录
  * [1,存储引擎的选择（MyISAM 和 Innodb）](#1)
    + [功能差异](#功能差异)
    + [选择依据](#选择依据)
    + [官方建议](#官方建议)
  * [2,字段设计](#字段设计)
  * [3,索引](#索引)
    + [使用索引为什么快？](#使用索引为什么快)
    + [索引的存储结构？](#索引的存储结构)
    + [索引的类型](#索引的类型)
    + [索引优化](#索引优化)
    + [索引失效原因](#索引失效原因)
  * [4,sql优化建议](#sql优化建议)
    + [1，sql的执行顺序](#2)
    + [2，超过三个表最好不要 join](#3)
    + [3，避免 SELECT *，从数据库里读出越多的数据，那么查询就会变得越慢](#4)
    + [4，尽可能的使用 NOT NULL列,可为NULL的列占用额外的空间,且在值比较和使用索引时需要特殊处理,影响性能](#5)
    + [5，用exists、not exists和in、not in相互替代](#6)
    + [6，使用exists替代distinct](#7)
    + [7，避免隐式数据类型转换](#8)
    + [8，分段查询](#9)
  * [Expalin 分析执行计划](#10)
## 存储引擎的选择（MyISAM 和 Innodb）<span id = 1 ></span>
`存储引擎：`MySQL中的数据、索引以及其他对象是如何存储的，是一套文件系统的实现。5.1之前默认存储引擎是MyISAM,5.1之后默认存储引擎是Innodb。
### 功能差异
区别| MyISAM | Innodb
---|---|---
文件格式|数据和索引是分别存储的。数据.MYD，索引.MYI|数据和索引是集中存储的.ibd
文件能否移动|能，一张表就对应.frm/MYD/MYI3个文件|否，因为关联的还有data下的其他文件
记录存储顺序|按记录插入顺序保存|按主键大小有序插入
空间碎片（删除记录并flush table表名之后，表文件大小不变）|产生，定时整理，使用命令optimize table表名实现| 安主键大小有序插入
事务|不支持|支持
外键|不支持|支持
锁支持|表级锁定|行级锁定、表级锁定。锁定粒度小，并发能力高

### 选择依据
MyISAM引擎设计简单，数据以紧密格式存储，所以某些读取场景下性能很好。

MyISAM：以读写插入为主的应用程序，比如博客系统、新闻门户网站。  
Innodb：更新（删除）操作频率也高，或者要保证数据的完整性；并发量高，支持事务和外键保证数据完整性。比如OA自动化办公系统。

### 官方建议
官方建议使用Innodb,上面只是告诉大家,数据引擎是可以选择,不过大多数情况还是不要选为妙
## 字段设计
1，数据库设计三大范式
- 第一范式：确保每列保持原子性
- 第二范式：确保表中的每列都和主键相关
- 第三范式（确保每列都和主键列直接相关，而不是间接相关）  
通常建议使用范式化设计,因为范式化通常会使得执行操作更快。但这并不是绝对的,范式化也是有缺点的,通常需要关联查询，不仅代价昂贵,也可能使一些索引策略无效。
2，单表字段不宜过多
3，使用小而简单的合适数据类型
4，尽量将列设置为NOT NULL
5，尽量使用整型做主键
## 索引
### 使用索引为什么快
- 索引相对于数据本身,数据量小
- 索引是有序的，可以快速确定数据位置
- InnoDB的表示索引组织表,表数据的分布按照主键排序
就好比书的目录,想要找到某一个内容,直接看目录便可找到对应的页
### 索引的存储结构
B+树、哈希。
Mysql中的主键索引用的是B+树结构,非主键索引可以选择B+树或者哈希。（建议使用B+索引）
>哈希索引的缺点：  
>1.无法用于排序  
 2.无法用于范围查询  
 3.数据量大时,可能会出现大量哈希碰撞,导致效率低下
 
### 索引的类型
`按作用分：`
- 主键索引:不解释,都知道
- 普通索引:没有特殊限制,允许重复的值   
- 唯一索引:不允许有重复的值,速度比普通索引略快 
- 全文索引:用作全文搜索匹配,但基本用不上,只能索引英文单词,而且操作代价很大
`按数据存储结构分类`
- 聚簇索引
> 定义：数据行的物理顺序与列值（一般是主键的那一列）的逻辑顺序相同，一个表中只能拥有一个聚集索引。

主键索引是聚簇索引,数据的存储顺序是和主键的顺序相同的
- 非聚簇索引
> 定义：该索引中索引的逻辑顺序与磁盘上行的物理存储顺序不同，一个表中可以拥有多个非聚集索引。

聚簇索引以外的索引都是非聚集索引,细分为普通索引、唯一索引、全文索引,它们也被称为二级索引。
[](https://pic1.zhimg.com/80/v2-e60cf162b49402bc91056d167bfb2460_hd.jpg)
主键索引的叶子节点存储的是"行指针",直接指向物理文件的数据行。

二级索引的叶子结点存储的是主键值
`覆盖索引`  
可直接从非主键索引直接获取数据无需回表的索引
假设t表有一个(clo1,clo2)的多列索引
```mysql
select clo1,clo2 from t where clo = 1
```
那么,使用这条sql查询,可直接从(clo1,clo2)索引树中获取数据,无需回表查询

因此我们需要尽可能的在select后只写必要的查询字段，以增加索引覆盖的几率。
`多列索引`  
使用多个列作为索引,比如(clo1,clo2)
使用场景:当查询中经常使用clo1和clo2作为查询条件时,可以使用组合索引,这种索引会比单列索引更快

需要注意的是,多列索引的使用遵循最左索引原则

假设创建了多列索引index(A,B,C)，那么其实相当于创建了如下三个组合索引：

1.index(A,B,C)

2.index(A,B)

3.index(A)

这就是最左索引原则，就是从最左侧开始组合。
### 索引优化
1.索引不是越多越好,索引是需要维护成本的  
2.在连接字段上应该建立索引  
3.尽量选择区分度高的列作为索引,区分度count(distinct col)/count(*)表示字段不重复的比例,比例越大扫描的记录数越少，状态值、性别字段等区分度低的字段不适合建索引  
4.几个字段经常同时以AND方式出现在Where子句中,可以建立复合索引,否则考虑单字段索引  
5.把计算放到业务层而不是数据库层  
6.如果有 order by、group by 的场景，请注意利用索引的有序性。  
### 索引失效原因
1.is null 和 is not null  
2.!= 和 <> (可用in代替)  
3."非独立列":索引列为表达式的一部分或是函数的参数  
4.like查询以%开头  
5.or (or两边的列都建立了索引则可以使用索引)  
6.类型不一致  
## sql优化建议
### 1，sql的执行顺序<span id = 2 ></span>
(1)FROM:数据从硬盘加载到数据缓冲区，方便对接下来的数据进行操作  
(2)ON:join on实现多表连接查询,先筛选on的条件,再连接表  
(3)JOIN:将join两边的表根据on的条件连接  
(4)WHERE:从基表或视图中选择满足条件的元组   
(5)GROUP BY:分组，一般和聚合函数一起使用  
(6)HAVING:在元组的基础上进行筛选，选出符合条件的元组（必须与GROUP BY连用）  
(7)SELECT:查询到得所有元组需要罗列的哪些列  
(8)DISTINCT:去重  
(9)UNION:将多个查询结果合并  
(10)ORDER BY：进行相应的排序   
(11)LIMIT:显示输出一条数据记录

- join on实现多表连接查询，推荐该种方式进行多表查询，不使用子查询(子查询会创建临时表,损耗性能)。
- 避免使用HAVING筛选数据,而是使用where
- ORDER BY后面的字段建立索引,利用索引的有序性排序,避免外部排序
- 如果明确知道只有一条结果返回，limit 1 能够提高效率
### 2，超过三个表最好不要 join<span id = 3 ></span>
### 3，避免 SELECT *，从数据库里读出越多的数据，那么查询就会变得越慢<span id = 4 ></span>
### 4，尽可能的使用 NOT NULL列,可为NULL的列占用额外的空间,且在值比较和使用索引时需要特殊处理,影响性能<span id = 5 ></span>
### 5，用exists、not exists和in、not in相互替代<span id = 6 ></span>
原则是哪个的子查询产生的结果集小，就选哪个
```mysql
select * from t1 where x in (select y from t2);
select * from t1 where exists (select null from t2 where y =x)
```
IN适合于外表大而内表小的情况；exists适合于外表小而内表大的情况
### 6，使用exists替代distinct<span id = 7 ></span>
当提交一个包含一对多表信息（比如部门表和雇员表）的查询时，避免在select子句中使用distinct，一般可以考虑使用exists代替，exists使查询更为迅速，因为子查询的条件一旦满足，立马返回结果。
低效写法：
```mysql
select distinct dept_no,dept_name from dept d,emp e where d.dept_no=e.dept_no
```
高效写法：
```mysql
select dept_no,dept_name from dept d where exists (select 'x' from emp e where e.dept_no=d.dept_no)
```
备注：其中x的意思是：因为exists只是看子查询是否有结果返回，而不关心返回的什么内容，因此建议写一个常量，性能较高！  
用exists的确可以替代distinct，不过以上方案仅适用dept_no为唯一主键的情况，如果要去掉重复记录，需要参照以下写法：
```mysql
select * from emp where dept_no exists (select Max(dept_no)) from dept d, emp e where e.dept_no=d.dept_no group by d.dept_no)
```
### 7，避免隐式数据类型转换<span id = 8 ></span>
隐式数据类型转换不能适用索引，导致全表扫描！t_tablename表的phonenumber字段为varchar类型
以下代码不符合规范：
```mysql
select column1 into i_l_variable1 from t_tablename where phonenumber=18519722169;
```
应编写如下：
```mysql
select column1 into i_lvariable1 from t_tablename where phonenumber='18519722169';
```
### 8，分段查询<span id = 9 ></span>
在一些查询页面中，当用户选择的时间范围过大，造成查询缓慢。主要的原因是扫描行数过多。这个时候可以通过程序，分段进行查询，循环遍历，将结果合并处理进行展示
## Expalin 分析执行计划
explain显示了mysql如何使用索引来处理select语句以及连接表。可以帮助选择更好的索引和写出更优化的查询语句。
例:
```mysql
explain SELECT user_name from sys_user where user_id <10
```
![](https://pic1.zhimg.com/80/v2-225b5ab0a6f9d8eb4c208551786b37e4_hd.jpg)
该语句连接类型为range,使用主键索引进行了范围查询,估计扫描了100行数据

更多含义详看下面表格从上可看出
![](https://pic4.zhimg.com/80/v2-ed8326097720340c80d151800d5691a7_hd.jpg)
![](https://pic3.zhimg.com/80/v2-2dccf2d8aa65065fb9d33f2c964db6f6_hd.jpg)

---
### EXPLAIN列的解释：<span id = 10 ></span>
####select_type
1） SIMPLE：简单的SELECT，不实用UNION或者子查询。  
2） PRIMARY：最外层SELECT。  
3） UNION：第二层，在SELECT之后使用了UNION。  
4） DEPENDENT UNION：UNION语句中的第二个SELECT，依赖于外部子查询。  
5） UNION RESULT：UNION的结果。  
6） SUBQUERY：子查询中的第一个SELECT。  
7） DEPENDENT SUBQUERY：子查询中的第一个SELECT，取决于外面的查询。  
8） DERIVED：导出表的SELECT（FROM子句的子查询）  

#### table
显示这一行的数据是关于哪张表的

#### type
这是重要的列，显示连接使用了何种类型。  
从最好到最差的连接类型：const、eq_reg、ref、range、index和ALL

Type：告诉我们对表使用的访问方式，主要包含如下集中类型。

1） all：全表扫描。  
2） const：读常量，最多只会有一条记录匹配，由于是常量，实际上只须要读一次。  
3） eq_ref：最多只会有一条匹配结果，一般是通过主键或唯一键索引来访问。  
4） fulltext：进行全文索引检索。  
5） index：全索引扫描。  
6） index_merge：查询中同时使用两个（或更多）索引，然后对索引结果进行合并（merge），再读取表数据。  
7） index_subquery：子查询中的返回结果字段组合是一个索引（或索引组合），但不是一个主键或唯一索引。  
8） rang：索引范围扫描。  
9） ref：Join语句中被驱动表索引引用的查询。  
10） ref_or_null：与ref的唯一区别就是在使用索引引用的查询之外再增加一个空值的查询。  
11） system：系统表，表中只有一行数据；   
12） unique_subquery：子查询中的返回结果字段组合是主键或唯一约束。  

#### possible_keys
显示可能应用在这张表中的索引。如果为空，没有可能的索引。可以为相关的域从WHERE语句中选择一个合适的语句

#### key
实际使用的索引。如果为NULL，则没有使用索引。很少的情况下，MYSQL会选择优化不足的索引。这种情况下，可以在SELECT语句中使用USE INDEX（indexname）来强制使用一个索引或者用IGNORE INDEX（indexname）来强制MYSQL忽略索引

#### key_len
使用的索引的长度。在不损失精确性的情况下，长度越短越好

### ref
显示索引的哪一列被使用了，如果可能的话，是一个常数

#### rows
MYSQL认为必须检查的用来返回请求数据的行数

#### Extra
关于MYSQL如何解析查询的额外信息。将在表4.3中讨论，但这里可以看到的坏的例子是Using temporary和Using filesort，意思MYSQL根本不能使用索引，结果是检索会很慢

Extra字段解释

Extra：查询中每一步实现的额外细节信息，主要会是以下内容。

Distinct：查找distinct 值，当mysql找到了第一条匹配的结果时，将停止该值的查询，转为后面其他值查询。

Full scan on NULL key：子查询中的一种优化方式，主要在遇到无法通过索引访问null值的使用。

Range checked for each record (index map: N)：通过 MySQL 官方手册的描述，当 MySQL Query Optimizer 没有发现好的可以使用的索引时，如果发现前面表的列值已知，部分索引可以使用。对前面表的每个行组合，MySQL检查是否可以使用range或 index_merge访问方法来索取行。

SELECT tables optimized away：当我们使用某些聚合函数来访问存在索引的某个字段时，MySQL Query Optimizer 会通过索引直接一次定位到所需的数据行完成整个查询。当然，前提是在 Query 中不能有 GROUP BY 操作。如使用MIN()或MAX()的时候。

Using filesort：当Query 中包含 ORDER BY 操作，而且无法利用索引完成排序操作的时候，MySQL Query Optimizer 不得不选择相应的排序算法来实现。

Using index：所需数据只需在 Index 即可全部获得，不须要再到表中取数据。

Using index for group-by：数据访问和 Using index 一样，所需数据只须要读取索引，当Query 中使用GROUP BY或DISTINCT 子句时，如果分组字段也在索引中，Extra中的信息就会是 Using index for group-by。

Using temporary：当 MySQL 在某些操作中必须使用临时表时，在 Extra 信息中就会出现Using temporary 。主要常见于 GROUP BY 和 ORDER BY 等操作中。

Using where：如果不读取表的所有数据，或不是仅仅通过索引就可以获取所有需要的数据，则会出现 Using where 信息。

Using where with pushed condition：这是一个仅仅在 NDBCluster存储引擎中才会出现的信息，而且还须要通过打开 Condition Pushdown 优化功能才可能被使用。控制参数为 engine_condition_pushdown 。

Impossible WHERE noticed after reading const tables：MySQL Query Optimizer 通过收集到的统计信息判断出不可能存在结果。

No tables：Query 语句中使用 FROM DUAL或不包含任何 FROM子句。

Not exists：在某些左连接中，MySQL Query Optimizer通过改变原有 Query 的组成而使用的优化方法，可以部分减少数据访问次数。