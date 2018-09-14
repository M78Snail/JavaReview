## Hadoop2.x HDFS新特性

* HDFS联邦
* HDFS HA（要用到zookeeper等）
* HDFS快照

### 回顾：HDFS两层模型

* Namespace:包括目录、文件和块。它支持所有命名空间相关的文件操作,如创建、删除、修改,查看所有文件和目录。

* Block Storage Service\(块存储服务\)包括两部分

![](/assets/importhad2.x 1.png)

1. 在 namenode中的块的管理
   1. 提供 datanode集群的注册、心跳检测等功能；
   2. 处理块的报告信息和维护块的位置信息
   3. 支持块相关的操作,如创建、删除、修改、获取块的位置信息
   4. 管理块的冗余信息、创建副本、删除多余的副本等。
2. 存储: datanode提供本地文件系统上块的存储、读写、访问等。

---

### 多 nannodes及 namespaces体系

* 注意“联邦”这个改治术语的含义

* 目的:水平扩展名称服务

* 使用多个独立的 namenode和 namespaces.每个 namenode是独立的,不需要和其它 namenode协调合作

* datanode作为统一的块存储设备被所有 namenode节点使用,

* 每一个 datanode节点都在所有的 namenode进行注册, datanode发送心跳信息、块报告到所有 namenode同时执行所有 namenode发来的命令

#### 块池\( Block Pool\)

* 块池是属于单个命名空间的一组块

* 每一个 datanode为所有的 block pool存储块

* Datanode是一个物理概念,而 block pod是一个重新将bock划分的逻铝概念

* 同一个 datanode中可以存着属于多个 block poo的多个块

* Block pool允许一个命名空间在不通知其他命名空间的情况下为一个新的 block创建

* 一个 Namenode失效不会影的其下的 datanode为其他 Namenode的服务

#### 格式化名称节点

1. 第一步：格式化其中一个节点，不指定clusterId则自动产生
   ```
   ./bin/hdfs namenode -format [-clusterId <cluster_id>]
   ```
2. 第二步：格式化另一个节点，注意clusterId要和之前的节点一样

   ```
   ./bin/hdfs namenode -format -clusterId <cluster_id>
   ```

---

### HDFS快照

* 设置一个目录为可快照
  ```
  hdfs dfadmin -allowSnapshot <path>
  ```

* 取消目录可快照
  ```
  hdfs dfadmin -disallowSnapshot <path>
  ```

* 生成快照
  ```
  hdfs dfs -createSnapshot <path> [<snapshotName>]
  ```

* 删除快照
  ```
  hdfs dfs -deleteSnapshot <path> <snapshotName>
  ```

* 列出所有可快照目录
  ```
  hdfs lsSnapshottableDir
  ```

* 比较快照之间的差异
  ```
  hdfs snapshotDiff <path> <fromSnapshot> <toSnapshot>
  ```





