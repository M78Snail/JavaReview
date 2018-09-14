## HDFS

* 提供分布式存储机制，提供可线性增长的海量存储能力
* 自动数据冗余，无须使用Raid，无须另行备份
* 为进一步分析计算提供数据基础

### HDFS体系结构

* NameNode
* DataNode
* 事务日志
* 映像文件
* SecondaryNameNode

读取数据流程：

1. 客户端要访问HDFS中的一个文件
2. 首先从NameNode获得组成这个文件的数据块位置列表
3. 根据列表知道存储数据块的DataNode
4. 访问DataNode获取数据
5. NameNode并不参与数据实际传输

![](/assets/importHap01.png)![](/assets/importhad02.png)

![](/assets/import.png)

#### NameNode

* 管理文件系统的命名空间
* 记录每个文件数据块在各个Datanode上的位置和副本信息
* 协调客户端对文件的访问
* 记录命名空间内的改动或空间本身属性的改动
* NameNode使用事务日志记录HDFS元数据的变化。使用映像文件存储文件系统的命名空间，包括文件映射，文件属性等

**tmp/dfs/name/current：**

```
edits  fsimage  fstime  VERSION
```

1. VERSION
   ```
   namespaceID=587961340                //命名空间标识
   cTime=0                              //分布式集群创建的时间
   storageType=NAME_NODE                //存储的目录结构用来存储NameNode
   layoutVersion=-32                    //
   ```
2. fsimage 文件系统的映像
3. edits 编辑日志

**tmp/dfs/namesecondary/current ：NameNode一个备份**

---

#### DataNode

* 负责所在物理节点的存储管理
* 一次写入，多次读取（不修改）
* 文件由数据块组成，典型的块大小是64MB
* 数据块尽量散步到各个节点

---



