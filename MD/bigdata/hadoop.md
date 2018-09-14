## 配置Hadoop 1.1.2

### 下载Hadoop

```
wget http://archive.apache.org/dist/hadoop/core/hadoop-1.1.2/hadoop-1.1.2-bin.tar.gz
```

### 修改配置文件

* **conf/hadoop-env.sh **
  ```
  export JAVA_HOME = /usr/local/java/jdk1.7.0_55
  ```
* **conf/core-site.xml**

  ```
  <configuration>
      <property>
          <name>fs.default.name</name>
          <value>hdfs://backup01:9000</value>             //名称节点在哪里，backup01主机名（同ip），在host文件中做解析
      </property>

      <property>
          <name>hadoop.tmp.dir</name>                     //hadoop临时目录
          <value>/home/duxiaoming/hadoop-1.1.2/tmp</value>
      </property>
  </configuration>
  ```

* **conf/hdfs-site.xml**

  ```
  <configuration>
      <property>
          <name>dfs.replication</name>               dfs服务器因子
          <value>1</value>                           复制1份
      </property>
  </configuration>
  ```

* **conf/mapred-site.xml**

  ```
  <configuration>
      <property>
          <name>mapred.job.tracker</name>
          <value>backup01:9001</value>
      </property>
  </configuration>
  ```

* **conf/masters**

  ```
  backup01
  ```

* **conf/slaves**

  ```
  backup02
  ```

* **vim /etc/hosts**

  ```
  192.168.25.135      backup01
  192.168.25.136      backup02
  ```

* 关闭防火墙

  ```
  chkconfig iptables off
  service iptables stop
  ```

### 格式化名称节点

```
bin/hadoop namenode -format
```

### 启动hadoop集群

* 免密码
  ```
  复制.ssh下面的 id_rsa.pub 内容到 authorized_keys 中
  ```
* 查看java启动了什么进程，要全部杀死才能启动
  ```
  /usr/local/java/jdk1.7.0_55/bin/jps
  kill -9 进程号
  ```
* 启动
  ```
   bin/start-all.sh
  ```
* 成功
  * backup01
    ```
    SecondaryNameNode
    JobTracker
    NameNode
    ```
  * backup02
    ```
    DataNode  
    TaskTracker
    ```

---

## 配置 Hadoop2.x

## 配置文件

配置文件多修改两个：

```
etc/hadoop/yarn-env.sh
etc/hadoop/yarn-site.xml
```

* etc/hadoop/hadoop-env.sh

  ```
  export JAVA_HOME = /usr/local/java/jdk1.7.0_55
  ```

* slaves

  ```
  h2
  h3
  ```

* core-site.xml

  ```
  创建好 /home/duxiaoming/hadoop-2.2.0/tmp 临时目录
  ```

* ```
  <configuration>
      <property>
          <name>fs.defaultFS</name>
          <value>hdfs://h1:9000</value>
      </property>
      <property>
          <name>io.file.buffer.size</name>
          <value>131072</value>
      </property>
      <property>
          <name>hadoop.tmp.file</name>
          <value>file:/home/h1/hadoop-2.2.0/tmp</value>
          <description>Abase for other temporary directions.</description>
      </property>
      <property>
          <name>hadoop.proxyuser.hduser.hosts</name>
          <value>*</value>
      </property>
      <property>
          <name>hadoop.proxyuser.hduser.groups</name>
          <value>*</value>
      </property>
  </configuration>
  ```
* hdfs-site.xml
  ```
  创建好 /home/duxiaoming/hadoop-2.2.0/name 临时目录
  创建好 /home/duxiaoming/hadoop-2.2.0/data 临时目录
  ```
* ```
  <configuration>
      <property>
          <name>fs.defaultFS</name>
          <value>hdfs://h1:9000</value>
      </property>
      <property>
          <name>io.file.buffer.size</name>
          <value>131072</value>
      </property>
      <property>
          <name>hadoop.tmp.file</name>
          <value>file:/home/h1/hadoop-2.2.0/tmp</value>
          <description>Abase for other temporary directions.</description>
      </property>
      <property>
          <name>hadoop.proxyuser.hduser.hosts</name>
          <value>*</value>
      </property>
      <property>
          <name>hadoop.proxyuser.hduser.groups</name>
          <value>*</value>
      </property>
      <property>
         <name>dfs.permissions</name>
         <value>false</value>
      </property>
  </configuration>
  ```
* mapred-site.xml

  ```
  <configuration>
      <property>
          <name>mapreduce.framework.name</name>
          <value>yarn</value>
      </property>
      <property>
          <name>io.file.buffer.size</name>
          <value>131072</value>
      </property>
      <property>
          <name>mapreduce.jobhistory.address</name>
          <value>h1:10020</value>
      </property>
      <property>
          <name>mapreduce.jobhistory.webapp.address</name>
          <value>h1:19888</value>
      </property>
  </configuration>
  ```

* yarn-site.xml

  ```
  <configuration>
      <property>
          <name>yarn.nodemanager.aux-services</name>
          <value>mapreduce_shuffle</value>
      </property>
      <property>
          <name>io.file.buffer.size</name>
          <value>131072</value>
      </property>
      <property>
          <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
          <value>org.apache.hadoop.mapred.ShuffleHandler</value>
      </property>
      <property>
          <name>yarn.resourcemanager.address</name>
          <value>h1:8032</value>
      </property>
      <property>
          <name>yarn.resourcemanager.scheduler.address</name>
          <value>h1:8030</value>
      </property>
      <property>
          <name>yarn.resourcemanager.resource-tracker.address</name>
          <value>h1:8031</value>
      </property>
      <property>
          <name>yarn.resourcemanager.admin.address</name>
          <value>h1:8033</value>
      </property>
      <property>
          <name>yarn.resourcemanager.webapp.address</name>
          <value>h1:8088</value>
      </property>
  </configuration>
  ```

* yarn-env.sh

  ```
  # some Java parameters
  export JAVA_HOME=/usr/local/java/jdk1.7.0_55
  ```

---

## 启动

### 格式化节点

```
./bin/hdfs namenode -format
```

### 启动hdfs

```
./sbin/start-dfs.sh
```

此时在h1上的进程有

1. NameNode
2. SeconddaryNameNode

在h2，h3上的进程有

1. DataNode

### 启动yarn

```
./sbin/start-yarn.sh
```

此时在h1上的进程有

1. NameNode
2. SeconddaryNameNode
3. ResourceManager

在h2，h3上的进程有

1. DataNode
2. NodeManager

---

## libhadoop.so.1.0.0在64位的问题

```
cd lib/native 
file ./libhadoop.so.1.0.0      //发现其是32位的文件
```

### 安装于编译有关的包（基于CentOS）

* yum install svn
* yum install autoconfautomake libtool cmake
* yum install ncurses-devel
* yum install openssl-devel
* yum install gcc\*
* 安装 maven  [http://archive.apache.org/dist/maven/maven-3/3.2.1/binaries/apache-maven-3.2.1-bin.tar.gz](http://archive.apache.org/dist/maven/maven-3/3.2.1/binaries/apache-maven-3.2.1-bin.tar.gz)

  * 修改环境变量（/home/h1/.bash\_profile）

    ```
    PATH=$PATH:$HOME/bin:/usr/local/apache-maven-3.2.1/bin
    JAVA_HOME=/usr/local/java/jdk1.7.0_55

    export JAVA_HOME

    export PATH
    ```

* 下载protobuf

  ```
  ./configure
  make
  make check
  make install
  ```

* 下载hadoop源码

  ```
  svn checkout http://svn.apache.org/repos/asf/hadoop/common/tags/release-2.3.0
  ```

* 重新编译本地库

  ```
  mvn package -Pdist,native-DskipTests-Dtar
  ```



