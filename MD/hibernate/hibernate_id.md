## **主键的生成策略**

1. increment:适用于short,int,long作为主键.不是使用的数据库自动增长机制.
    * Hibernate中提供的一种增长机制.
        * 先进行查询 :select max(id) from user;
        * 再进行插入 :获得最大值+1作为新的记录的主键.

    * 问题:不能在集群环境下或者有并发访问的情况下使用.

2. identity:适用于short,int,long作为主键。但是这个必须使用在有自动增长数据库中.采用的是数据库底层的自动增长机制.
    * 底层使用的是数据库的自动增长(auto_increment).像Oracle数据库没有自动增长.

3. sequence:适用于short,int,long作为主键.底层使用的是序列的增长方式.
    * Oracle数据库底层没有自动增长,想自动增长需要使用序列.

4. uuid:适用于char,varchar类型的作为主键.
    * 使用随机的字符串作为主键.

5. native:本地策略.根据底层的数据库不同,自动选择适用于该种数据库的生成策略.(short,int,long)
    * 如果底层使用的MySQL数据库:相当于identity.
    * 如果底层使用Oracle数据库:相当于sequence.

6. assigned:主键的生成不用Hibernate管理了.必须手动设置主键. 