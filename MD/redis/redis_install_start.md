# Redis的安装

Redis是c语言开发的。

安装redis需要c语言的编译环境。如果没有gcc需要在线安装。

```
yum install gcc-c++
```

安装步骤：

1. 第一步：redis的源码包上传到linux系统。
2. 第二步：解压缩redis。
3. 第三步：编译。进入redis源码目录。make 
4. 第四步：安装。make install PREFIX=/usr/local/redis（PREFIX参数指定redis的安装目录。一般软件安装到/usr目录下）

## 单机启动

```
./redis-server redis.conf
```



# Redis集群的搭建

**/usr/local/redis-cluster**

```java
./redis-trib.rb create --replicas 1 
 192.168.25.128:7001 
 192.168.25.128:7002 
 192.168.25.128:7003
 192.168.25.128:7004 
 192.168.25.128:7005 
 192.168.25.128:7006
```

## 集群连接：
Redis集群中至少应该有三个节点。要保证集群的高可用，需要每个节点有一个备份机。
Redis集群至少需要6台服务器。

搭建伪分布式。可以使用一台虚拟机运行6个redis实例。需要修改redis的端口号7001-7006

### 1、使用ruby脚本搭建集群。需要ruby的运行环境。

```
安装ruby
yum install ruby
yum install rubygems
```

### 2、安装ruby脚本运行使用的包。

```
[root@localhost ~]# gem install redis-3.0.0.gem 
Successfully installed redis-3.0.0
1 gem installed
Installing ri documentation for redis-3.0.0...
Installing RDoc documentation for redis-3.0.0...
[root@localhost ~]# 

[root@localhost ~]# cd redis-3.0.0/src
[root@localhost src]# ll *.rb
-rwxrwxr-x. 1 root root 48141 Apr  1  2015 redis-trib.rb
```

### 3、搭建伪分布式

1. 第一步：创建6个redis实例，每个实例运行在不同的端口。需要修改redis.conf配置文件。配置文件中还需要把cluster-enabled yes前的注释去掉。
2. 第二步：启动每个redis实例。
3. 第三步：使用ruby脚本搭建集群。
 ```
./redis-trib.rb create --replicas 1 192.168.25.153:7001 192.168.25.153:7002 192.168.25.153:7003 192.168.25.153:7004 192.168.25.153:7005 192.168.25.153:7006
 ```
4. 第四步：创建关闭集群的脚本：
```
[root@localhost redis-cluster]# vim shutdow-all.sh
redis01/redis-cli -p 7001 shutdown
redis01/redis-cli -p 7002 shutdown
redis01/redis-cli -p 7003 shutdown
redis01/redis-cli -p 7004 shutdown
redis01/redis-cli -p 7005 shutdown
redis01/redis-cli -p 7006 shutdown
[root@localhost redis-cluster]# chmod u+x shutdow-all.sh 
```
5. 第五步：Redis-cli连接集群
```
redis01/redis-cli -p 7006 -c
```

-c：代表连接的是redis集群



