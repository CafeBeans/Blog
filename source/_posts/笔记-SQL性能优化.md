---
title: '笔记|SQL性能优化'
date: 2017-06-14 09:47:48
categories: 技术探究
tags:
- 性能调优
- MySQL
---

前不久在图书馆借了一本《MySQL技术精粹》，现在快要归还了，打算做个笔记，主要内容为之前几乎没有接触过的SQL性能优化部分。因为一直在用MySQL，也写过不少SQL语句，可是自己平常写的小项目中数据量不会很大，也没有投入生产环境，因此对性能方面要求不高，然而在实际应用中，面对海量数据存储的应用，为了不让SQL执行的效率制约应用的效率，就不得不优化SQL了。对于后端开发者而言，有必要了解关于SQL优化的相关知识。
<!-- more -->
### 笔记
#### MySQL Query Optimizer
在MySQL中有个专门负责优化SELECT语句的优化器模块——Query Optimizer，能将客户端传送过来的查询语句，结合数据库系统收集的各种统计信息，为客户端的SQL进行查询优化（常量转化，无效内容删除，常量计算等等），最终得出执行计划。
Query Optimizer的所有工作都基于QueryTree，其记录了完成一个Query所需要经历的步骤，每一步的数据来源以及处理方式等。
因此，MySQL数据库能在一定程度上帮助我们开发者优化SELECT语句。

#### SQL优化查询语句基本思路
##### 优化更需要优化的查询
先从对整个系统的影响来考虑，什么样的优化能给系统整体带来更大的受益，就是更需要优化的查询。例如高并发低消耗的查询就远比低并发高消耗的查询对系统影响大。
假设A查询每分钟执行10000次，每次30个读写；B查每分钟执行100次，每次3000个读写。两个查询总操作数相同，但是在优化的时候，A查询读写减少5个，每分钟总操作数减少50000次；而B查询要达到同样的效果，却要减少500个读写。可见优化更需要优化的查询不仅实现难度低，而且带来的效果是可观的。

##### 定位优化对象的性能瓶颈
确定优化对象后，就要定位优化对象的性能瓶颈了，是读写问题呢？还是CPU耗损问题呢？是数据运算耗费资源呢？还是数据访问耗费太多的时间？使用PROFILING能很清楚的找出一个查询瓶颈所在。

##### 明确优化目标
当找到性能瓶颈之后，就要制定优化目标。
先搞清楚数据库目前的状态，确定数据库所能承受的最大压力
然后清楚数据库中与该查询相关的数据库对象信息，确定查询消耗资源的情况
接着了解查询实现的功能，根据功能点在系统中的地位得出可以占用的系统资源比例。
最后得出查询的性能范围，也是优化目标范围。

##### 充分使用EXPLAIN和PROFILE工具对SQL语句进行分析
确定优化目标后，开始实际优化操作。借助EXPLAIN工具查看查询的执行计划，并不断调整查询语句，以期达到优化目标，最后得出优化的查询语句

#### 利用EXPLAIN分析查询语句
![](/images/explain.png)
解释：
- ID
ID列中的数据为一组数字，表示执行select语句的顺序
ID值相同时，执行顺序由上至下
ID值越大优先级越高，越先被执行
- SELECT_TYPE
SIMPLE：不包含子查询或是UNION操作的查询
PRIMARY：查询中如果包含任何子查询，那么最外层的查询则被标记为PRIMARY
SUBQUERY：SELECT 列表中的子查询
DEPENDENT SUBQUERY：被别的查询所依赖的子查询
UNION：union操作的第二个或是之后的查询的值为union
DEPENDENT UNION：当union作为子查询时，第二或者是第二个后的查询的值
UNION RESULT：union产生的结果集
DERIVED：出现在from子句中的子查询
- TABLE
输出数据行所在的表的名称或别名：
由ID为M,N查询union产生的结果集： unionM,N
由ID为N的查询产生的结果: derivedN/subqueryN
- PARTITIONS
对于分区表，显示查询的分区ID
对于非分区表，显示为NULL
- TYPE（类型性能是依次降低的）
system，这是const连接类型的一个特例，当查询的表只有一行时使用。
const，表中有且只有一个匹配的行时使用，如对主键或是唯一索引的查询，这是效率最高的联接方式。
eq_ref，唯一索引或者是主键索引查找，对于每个索引键，表中只有一条记录与之匹配
ref，非唯一索引查找，返回匹配某个单独值的所有行。
ref_or_null，类似于ref类型的查询，但是附加了对NULL值列的查询。
index_merge，该联接类型表示使用了索引合并优化方法。
range，索引范围扫描，常见于between、>、<、这样的查询条件。
index，full index scan 全索引扫描，同ALL的区别是，遍历的是索引树。
all，full table scan 全表扫描，这是效率最差的联接方式。
- EXTRA
distinct，优化distinct操作，在找到第一匹配的元组后即停止找同样值的动作
not exists，使用not exists来优化查询
using filesort，使用额外操作进行排序，通常会出现在order by 或者 group by查询中。
using index，使用了覆盖索引进行查询。
using temporary，需要使用临时表来处理查询，常见于排序，子查询，分组查询
using where，需要在mysql服务器层使用where条件来过滤数据。
select tables optimized away，直接通过索引来获得数据，不用访问表。
- POSSIBLE_KEYS
指出mysql能使用那些索引来优化查询，查询列所涉及到的列上的索引都会被列出，但不一定会被使用
- KEYS
查询优化器优化查询实际所使用的索引，如果查询过程中没有用到索引，则会显示为NULL，此处的值也可能不出现在POSSIBLE_KEYS，则说明，使用了覆盖索引。
- KEY_LEN
表示索引字段的最大可能长度。
此值的长度有字段定义计算而来，并非数据的实际长度。
- REF
表示那些列或常量被用于查找索引列上的值
- ROWS
表示mysql通过索引统计的信息，估算出的所需读取的行数。是一个不十分准确的值。
- FILTERED
表示反悔结果的行数占需读取行数的百分比，越大越好，也并不十分准确。

#### 利用Profiling分析查询语句
通过该工具可以获取查询在整个过程中多种资源的消耗情况，例如内存消耗、I/O消耗和CPU消耗等
语法:
SHOW PROFILE [type[,type]...]
[FOR QUERY n]
[LIMIT row_count [OFFSET offset]]

其中type取值有：
- ALL：显示所有信息
- BLOCK IO：显示输入输出操作阻塞的数量
- CONTEXT SWITCHES：显示自动或非自动CONTEXT SWITCHES的数量
- CPU：显示系统和CPU使用的时间
- IPC：显示消息发送和接收的数量
- MEMORY：内存的信息
- PAGE FAULTS：显示主要的PAGE FAULTS数量
- SOURCE：显示函数的名称，并且显示函数所在文件的名字和行数
- SWAPS：显示SWAP数量

使用步骤：
1.查询profiling是否开启
```
select @@profiling;
```
2.开启profiling
```
SET profiling = 1;
```
3.执行查询语句
4.查看Profiling情况，得出查询语句在Profiling中的ID
```
show profiles;
```
5.根据ID分析该查询资源的消耗情况
```
show profile [type] for query "ID" 
```

#### 合理地使用索引

1.使用LIKE关键字的查询语句，匹配字符串的第一个字符不能为“%”，否则索引失效
2.使用多列索引的查询语句，查询条件需要有这些字段中的第一个字段，否则索引失效
3.使用OR关键字的查询语句，若查询条件只有OR关键字，则OR前后的两个条件中的列都必须有索引，否则索引失效

#### 不同类型SQL语句优化方法

##### 优化INSERT语句
1.同时插入多行数据，减少客户端与数据库服务器之间的连接和关闭等操作
2.对于MyISAM表，可以使用INSERT DELAYED语句提升执行速度，不过数据不会立即插入到表，而是在内存中等待，空闲时插入，若系统崩溃将会丢失数据
3.锁定表

##### 优化ORDER BY语句
- 采用索引进行优化

1.对ORDER BY + LIMIT组合的索引优化
```
SELECT [column1] …… FROM [TABLE] ORDER BY[sort] LIMIT[offset],[LIMIT];
```
该语句优化只需要在[sort]上建立索引

2.对WHERE+ORDER BY+LIMIT组合的索引优化
```
SELECT [column1] …… FROM [TABLE] WHERE [columnX] = [value] ORDER BY [sort] LIMIT[offset],[LIMIT];
```
这时，可以建立联合索引(columnX,sort)

3.不要对WHERE和ORDER BY的选项使用表达式或函数

- 不应该使用索引的情况

1.order by 的字段混合使用asc和desc
2.where字句使用的字段和order by 的字段不一样
3.对不同的关键字使用order by 排序

##### 优化GROUP BY语句
通过指定ORDER BY NULL可以禁止自动排序，从而节省损耗，优化GROUP BY语句

##### 优化嵌套查询
使用连接查询 JOIN 代替子查询，避免在内存中创建临时表

##### 优化OR条件
对OR的每个条件列添加索引

##### 优化插入记录的速度
插入记录时，影响插入速度的主要是索引、唯一性校验、一次插入记录条数等等
- MyISAM表
1.禁用索引
2.禁用唯一性检查
3.一条INSERT语句插入多条记录
4.使用LOAD命令

- InnoDB表
1.禁用唯一性检查
2.禁用外键检查
3.禁止自动提交

#### 优化数据库结构

1.对多字段的表进行拆分，冷热分离；查询时使用联合查询
2.对经常联合查询的表建立中间表
3.增加冗余字段，减少连接操作

#### 分析表、检查表和优化表

1.分析表ANALYZE TABLE分析关键字的分布
2.检查表CHECK TABLE检查表是否存在错误
3.优化表OPTIMIZE TABLE消除删除或者更新造成的空间浪费

#### 小结
本文主要记录了SQL优化的基本思路，使用EXPLAIN与Profiling进行性能分析要点，关于合理添加索引的建议；
以及常用SQL语句的优化方法和数据库结构的优化方法。

### 更多
[MYSQL性能优化的最佳20+条经验](http://coolshell.cn/articles/1846.html)
[数据库性能优化之SQL语句优化1](https://my.oschina.net/xianggao/blog/87216)