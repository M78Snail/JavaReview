## HDFS的可靠性

* 冗余副本策略
* 机架策略
* 心跳机制
* 安全模式
* 校验和
* 回收站
* 元数据保护
* 快照机制

### 冗余副本策略

* 可以在hdfs-site.xml中设置复制因子指定副本数量
* 所有数据块都有副本
* DataNode启动时，遍历本地文件系统，产生一份hdfs数据块和本地文件的对应关系列表（blockreport）汇报给NameNode

### 机架策略

* 集群一般放在不同机架上，机架间带宽要比机架内带宽要小
* HDFS的“机架感知”
* 一般在本机架存放一个副本，在其他机架再存放别的副本，这样可以防止机架失效时丢失数据，也可以提高带宽利用率

### 心跳机制

* NameNode周期性从DataNode接收心跳信号和块报告
* NameNode根据块报告验证元数据
* 没有按时发送心跳的DataNode会被标记为宕机，不会再给它任何I/O请求
* 如果DataNode失效造成副本数量下降，并且低于预先设置的阀值，NameNode会检测出这些数据块，并在合适的时机进行重新复制
* 引发重新复制的原因还包括数据副本本身损坏，磁盘错误，复制因子被增大等

### 安全模式

* NameNode启动时会先经过一个“安全模式”阶段
* 安全模式阶段不会产生数据写
* 在此阶段NameNode收集各个DataNode的报告，当数据块达到最小副本数以上时，会被认为是“安全”的
* 在一定比例（可设置）的数据块被确定为“安全”后，再过若干时间，安全模式结束
* 当检测到副本数不足的数据块时，该块会被复制直到达到最小吧、副本数
  ```
  bin/hadoop dfsadmin -safemode enter    进入安全模式
  bin/hadoop dfsadmin -safemode leave    推出安全模式
  ```

### 校验和

* 在文件创立时,每个数据块都产生校验和
* 校验和保存在meta文件内
* 客户端获取数据时可以检查校验和是否相同,从而发现数据块是否损坏
* 如果正在读取的数据块损坏,则可以继续读取其它副本

### 回收站

* 刪除文件时,其实是放入回收站/ trash
* 回收站里的文件可以快速恢复
* 可以设置一个时间阈值,当回收站里文件的存放时间超过这个阈值,就被彻底删除
* 打开回收站功能

  * 在conf/core-site.xml添加配置

    ```
    <property>
        <name>fs.trash.interval</name>
        <value>1000</value>
        <description>
            Number of minutes between trashcheckpoints.If zero,the feature is disabled
        </description>
    </property>
    ```

  * 重启集群

    ```
    bin/stop-all.sh
    bin/start-all.sh
    ```

  * 恢复和清空

    ```
    bin/hadoop fs -mv ./.Trash/Current/user/duxiaoming/in ./
    bin/hadoop fs -expunge
    ```

### 元数据保护

* 映像文件和事务日志是NameNode的核心数据。可以配置为拥有多个副本
* 副本会降低NameNode的处理速度，但增加安全性
* NameNode依然是单点，如果发生故障要手工切换

### 快照机制

* 支持存储某个时间点的映像，需要时可以使数据重返这个时间点的状态
* Hadoop目前还不支持快照，已经列入开发计划，传说在Hadoop 2.x某版本里将获得此功能





