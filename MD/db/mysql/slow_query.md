# 谈谈SQL慢查询的解决思路

### **慢SQL的系统表现**

首先，我们如何判别系统中遇到了SQL慢查询问题？个人认为慢SQL有如下三个特征：

> 1，数据库CPU负载高。一般是查询语句中有很多计算逻辑，导致数据库cpu负载。
>
> 2，IO负载高导致服务器卡住。这个一般和全表查询没索引有关系。
>
> 3，查询语句正常，索引正常但是还是慢。如果表面上索引正常，但是查询慢，需要看看是否索引没有生效。

### **开启SQL慢查询的日志**

如果你的系统出现了上述情况，并且你不是用的阿里云的RDS这样的产品，那么下一步就需要打开Mysql的慢查询日志来进一步定位问题。MySQL 提供了慢查询日志，这个日志会记录所有执行时间超过 long_query_time（默认是10s）的 SQL 及相关的信息。

要开启日志，需要在 MySQL 的配置文件 my.cnf 的 [mysqld] 项下配置慢查询日志开启，如下所示：

```
[mysqld]slow_query_log=1
slow_query_log_file=/var/log/mysql/log-slow-queries.log
long_query_time=2
```

在实际项目中，由于生成的慢查询的日志可能会特别大，分析起来不是很方便，所以Mysql官方也提供了 **mysqldumpslow** 这个工具，方便我们分析慢查询日志，感兴趣的同学可以自行到Mysql官方进行查阅。

### SQL调优

有些SQL虽然出现在慢查询日志中，但未必是其本身的性能问题，可能是因为锁等待，服务器压力高等等。需要分析SQL语句真实的执行计划，而不是看重新执行一遍SQL时，花费了多少时间，由自带的慢查询日志或者开源的慢查询系统定位到具体的出问题的SQL，然后使用Explain工具来逐步调优，了解 MySQL 在执行这条数据时的一些细节，比如是否进行了优化、是否使用了索引等等。基于 Explain 的返回结果我们就可以根据 MySQL 的执行细节进一步分析是否应该优化搜索、怎样优化索引。

关于索引的创建及优化原则，个人特别推荐美团点评技术团队的几点总结，讲得特别好，特地引用一下：

> 1. 最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整；
> 2. =和in可以乱序，比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式；
> 3. 尽量选择区分度高的列作为索引,区分度的公式是count(distinct col)/count(*)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录；
> 4. 索引列不能参与计算，保持列“干净”，比如from_unixtime(create_time) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成create_time = unix_timestamp(’2014-05-29’);
> 5. 尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可。

### **一点总结**

基于本文的思路，关于SQL慢查询的解决可以按照以下的步骤执行：

1. 打开慢日志查询，确定是否有SQL语句占用了过多资源，如果是，在不改变业务原意的前提下，对insert、group by、order by、join等语句进行优化。

2. 考虑调整MySQL的系统参数： innodb_buffer_pool_size、innodb_log_file_size、table_cache等。

3. 确定是否是因为高并发引起行锁的超时问题。

4. 如果数据量过大，需要考虑进一步的分库分表。