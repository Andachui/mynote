**分享概要：**

1. **数据库不清楚的点**

## **一、数据库不清楚的点**
[详细请看](https://note.youdao.com/ynoteshare1/index.html?id=8df8c92eea0b21258dab5fc84e61171f&type=notebook#/wcp1587359222888640)
---
### 分配内存选择：
前缀索引  
语法：index(field(10))，使用字段值的前10个字符建立索引，默认是使用字段的全部
内容建立索引。  
前提：前缀的标识度高。比如密码就适合建立前缀索引，因为密码几乎各不相同。  
实操的难度：在于前缀截取的长度。  
我们可以利用select count(*)/count(distinct left(password,prefixLen));，通
过从调整prefixLen的值（从1自增）查看不同前缀长度的一个平均匹配度，接近1时就
可以了（表示一个密码的前prefixLen个字符几乎能确定唯一一条记录）  

什么是最左前缀原则？什么是最左匹配原则  
顾名思义，就是最左优先，在创建多列索引时，要根据业务需求，where子句中使用最
频繁的一列放在最左边。  
最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between
、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)
顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序
可以任意调整。  
=和in可以乱序，比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，
mysql的查询优化器会帮你优化成索引可以识别的形式
### 锁：
对MySQL的锁了解吗？  
当数据库有并发事务的时候，可能会产生数据的不一致，这时候需要一些机制来保证访问
的次序，锁机制就是这样的一个机制。  
就像酒店的房间，如果大家随意进出，就会出现多人抢夺同一个房间的情况，而在房间上
装上锁，申请到钥匙的人才可以入住并且将房间锁起来，其他人只有等他使用完毕才可以
再次使用。

隔离级别与锁的关系  
在Read Uncommitted级别下，读取数据不需要加共享锁，这样就不会跟被修改的数据上的
排他锁冲突  
在Read Committed级别下，读操作需要加共享锁，但是在语句执行完以后释放共享锁；  
在Repeatable Read级别下，读操作需要加共享锁，但是在事务提交之前并不释放共享锁
，也就是必须等待事务执行完毕以后才释放共享锁。  
SERIALIZABLE 是限制性最强的隔离级别，因为该级别锁定整个范围的键，并一直持有锁
，直到事务完成。  

按照锁的粒度分数据库锁有哪些？锁机制与InnoDB锁算法  
在关系型数据库中，可以按照锁的粒度把数据库锁分为行级锁(INNODB引擎)、表级
锁(MYISAM引擎)和页级锁(BDB引擎 )。
  
MyISAM和InnoDB存储引擎使用的锁：  
MyISAM采用表级锁(table-level locking)。  
InnoDB支持行级锁(row-level locking)和表级锁，默认为行级锁  
行级锁，表级锁和页级锁对比  
行级锁 行级锁是Mysql中锁定粒度最细的一种锁，表示只针对当前操作的行进行加锁。
行级锁能大大减少数据库操作的冲突。其加锁粒度最小，但加锁的开销也最大。行级锁
分为共享锁 和 排他锁。  
特点：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度
也最高。  
表级锁：表级锁是MySQL中锁定粒度最大的一种锁，表示对当前操作的整张表加锁，它实
现简单，资源消耗较少，被大部分MySQL引擎支持。最常使用的MYISAM与INNODB都支持
表级锁定。表级锁定分为表共享读锁（共享锁）与表独占写锁（排他锁）。  
特点：开销小，加锁快；不会出现死锁；锁定粒度大，发出锁冲突的概率最高，并发度
最低。  
页级锁：页级锁是MySQL中锁定粒度介于行级锁和表级锁中间的一种锁。表级锁速度快，
但冲突多，行级冲突少，但速度慢。所以取了折衷的页级，一次锁定相邻的一组记录。  
特点：开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之
间，并发度一般  
从锁的类别上分MySQL都有哪些锁呢？像上面那样子进行锁定岂不是有点阻碍并发效率了  
从锁的类别上来讲，有共享锁和排他锁。  
共享锁: 又叫做读锁。 当用户要进行数据的读取时，对数据加上共享锁。共享锁可以
同时加上多个。  
排他锁: 又叫做写锁。 当用户要进行数据的写入时，对数据加上排他锁。排他锁只可
以加一个，他和其他的排他锁，共享锁都相斥。  
用上面的例子来说就是用户的行为有两种，一种是来看房，多个用户一起看房是可以接
受的。 一种是真正的入住一晚，在这期间，无论是想入住的还是想看房的都不可以。  
锁的粒度取决于具体的存储引擎，InnoDB实现了行级锁，页级锁，表级锁。  
他们的加锁开销从大到小，并发能力也是从大到小。  
MySQL中InnoDB引擎的行锁是怎么实现的？  
答：InnoDB是基于索引来完成行锁  
例: select * from tab_with_index where id = 1 for update;  
for update 可以根据条件来完成行锁锁定，并且 id 是有索引键的列，如果 
id 不是索引键那么InnoDB将完成表锁，并发将无从谈起  
InnoDB存储引擎的锁的算法有三种  
Record lock：单个行记录上的锁  
Gap lock：间隙锁，锁定一个范围，不包括记录本身  
Next-key lock：record+gap 锁定一个范围，包含记录本身  

相关知识点：  
innodb对于行的查询使用next-key lock  
Next-locking keying为了解决Phantom Problem幻读问题  
当查询的索引含有唯一属性时，将next-key lock降级为record key  
Gap锁设计的目的是为了阻止多个事务将记录插入到同一范围内，而这会导致幻读问题
的产生  
有两种方式显式关闭gap锁：（除了外键约束和唯一性检查外，其余情况仅使用
record lock） A. 将事务隔离级别设置为RC B. 将参数innodb_locks_unsafe_for_binlog
设置为1  

### 分页优化
超大的分页一般从两个方向上来解决.  
数据库层面,这也是我们主要集中关注的(虽然收效没那么大),类似于select * 
from table where age > 20 limit 1000000,10这种查询其实也是有可以优化的余
地的. 这条语句需要load1000000数据然后基本上全部丢弃,只取10条当然比较慢. 
当时我们可以修改为select * from table where id in (select id from table 
where age > 20 limit 1000000,10).这样虽然也load了一百万的数据,但是由于索
引覆盖,要查询的所有字段都在索引中,所以速度会很快. 同时如果ID连续的好,我们
还可以select * from table where id > 1000000 limit 10,效率也是不错的,
优化的可能性有许多种,但是核心思想都一样,就是减少load的数据.  
从需求的角度减少这种请求…主要是不做类似的需求(直接跳转到几百万页之后的具体某
一页.只允许逐页查看或者按照给定的路线走,这样可预测,可缓存)以及防止ID泄漏且
连续被人恶意攻击.  
解决超大分页,其实主要是靠缓存,可预测性的提前查到内容,缓存至redis等k-V数据库
中,直接返回即可.  
在阿里巴巴《Java开发手册》中,对超大分页的解决办法是类似于上面提到的第一种.
>【推荐】利用延迟关联或者子查询优化超多分页场景。 说明：MySQL并不是跳过offset行，而是取offset+N行，然后返回放弃前offset行，返回N行，那当offset特别大的时候，效率就非常的低下，要么控制返回的总页数，要么对超过特定阈值的页数进行SQL改写。 正例：先快速定位需要获取的id段，然后再关联：  
>SELECT a.* FROM 表1 a,(select id from 表1 where 条件 LIMIT 100000,20) b where a.id=b.id

自己的一点想法：  
这里也可以用记录上次查询最大id，最为下一次查询的条件进行过滤，也会减少查询数量

### 慢查询优化
在业务系统中，除了使用主键进行的查询，其他的我都会在测试库上测试其耗时，
慢查询的统计主要由运维在做，会定期将业务中的慢查询反馈给我们。  
慢查询的优化首先要搞明白慢的原因是什么？ 是查询条件没有命中索引？是load了不
需要的数据列？还是数据量太大？  
所以优化也是针对这三个方向来的，  
首先分析语句，看看是否load了额外的数据，可能是查询了多余的行并且抛弃掉了，
可能是加载了许多结果中并不需要的列，对语句进行分析以及重写。  
分析语句的执行计划，然后获得其使用索引的情况，之后修改语句或者修改索引，使得
语句可以尽可能的命中索引。  
如果对语句的优化已经无法进行，可以考虑表中的数据量是否太大，如果是的话可
以进行横向或者纵向的分表。

1. 访问数据太多导致查询性能下降
2. 确定应用程序是否在检索大量超过需要的数据，可能是太多行或列
3. 确认MySQL服务器是否在分析大量不必要的数据行
4. 避免犯如下SQL语句错误
5. 查询不需要的数据。解决办法：使用limit解决
6. 多表关联返回全部列。解决办法：指定列名
7. 总是返回全部列。解决办法：避免使用SELECT *
8. 重复查询相同的数据。解决办法：可以缓存数据，下次直接读取缓存
9. 是否在扫描额外的记录。解决办法：
10. 使用explain进行分析，如果发现查询需要扫描大量的数据，但只返回少数的行，
可以通过如下技巧去优化：
11. 使用索引覆盖扫描，把所有的列都放到索引中，这样存储引擎不需要回表获取对应
行就可以返回结果。
12. 改变数据库和表的结构，修改数据表范式
13. 重写SQL语句，让优化器可以以更优的方式执行查询

### EXISTS用法
比较使用 EXISTS 和 IN 的查询。注意两个查询返回相同的结果，如示例。
```
select * from TableIn where exists(select BID from TableEx where BNAME=TableIn.ANAME)
select * from TableIn where ANAME in(select BNAME from TableEx)
```
EXISTS(包括 NOT EXISTS )子句的返回值是一个BOOL值。 EXISTS内部有一个子查询
语句(SELECT ... FROM...)， 我将其称为EXIST的内查询语句。其内查询语句返回
一个结果集。 EXISTS子句根据其内查询语句的结果集空或者非空，返回一个布尔值。

一种通俗的可以理解为：将外查询表的每一行，代入内查询作为检验，如果内查询返回
的结果取非空值，则EXISTS子句返回TRUE，这一行行可作为外查询的结果行，否则不能
作为结果（两次嵌套循环，对比内外）。

### cross join
MySQL cross join是mysql中的一种连接方式，区别于内连接和外连接，对于cross join
连接来说，其实使用的就是笛卡尔连接。在MySQL中，当CROSS JOIN不使用WHERE子句时
，CROSS JOIN产生了一个结果集，该结果集是两个关联表的行的乘积。通常，如果每个
表分别具有n和m行，则结果集将具有n*m行

### BDB(Berkeley)引擎
页级锁用在BDB!!!!!!!

该存储引擎支持COMMIT和ROLLBACK等其他事务特性。该引擎在包括MySQL 5.1及其
以上版本的数据库中不再支持。

嵌入式：bdb提供了一系列应用程序接口（API），调用这些接口很简单，应用程序
和bdb所提供的库一起编译/链接成为可执行程序  
NOSQL：bdb不支持SQL语言，它对数据的管理很简单，bdb数据库包含若干条记
录，每条记录由关键字和数据（key-value）两部分构成。数据可以是简单的数据类
型，也可以是复杂的数据类型，例如C语言的结构体，bdb对数据类型不做任何解释
，完全由程序员自行处理，典型的C语言指针的自由风格

### int长度问题
疑问：

mysql的字段，unsigned int(4), 和unsinged int(5), 能存储的数值范围是否相同。如果不同，分别是多大？

答：

无论是int(4), int(5), 存储的都是4字节无符号整数， 也就是0~2^32。。但是，当数字不足4位或5位时，前面会用0补齐。

MySQL支持选择在该类型关键字后面的括号内指定整数值的显示宽度(例如，INT(4))。

该可选显示宽度规定用于显示宽度小于指定的列宽度的值时从左侧填满宽度。

显示宽度并不限制可以在列内保存的值的范围，也不限制超过列的指定宽度的值的显示。 
也就是说，int的长度并不影响数据的存储精度，长度只和显示有关

>浮点型"的长度是用来限制数字存储范围的. 比如 float(3,2) 只能够写入 0.00~999.99.  
>"整型"的长度并不会限制存储的数字范围. 比如, int 和 int(3) 的存储范围都是 -2147483648 ~ 2147483647, int unsigned 和 int(3) unsigned 的存储范围都是0 ~ 4294967295
