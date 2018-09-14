## HDFS文件操作

* 命令行方式
* API方式（Java ）

### 命令行方式

* 列出HDFS下的文件
  > 注意，Hadoop没有当前目录的概念，也没有cd命令
* ```
  bin/hadoop fs -ls ./
  bin/hadoop fs -ls ./.Trash
  ```
* 上传文件到HDFS

  ```
  bin/hadoop fs -put ../file.gz ./in
  ```

* 数据写在了哪儿（从OS看）

  ```
  /tmp/dfs/data/current    目录下
  ```

* 将HDFS的文件复制到本地

  ```
  bin/hadoop fs -get ./in/test1.txt ../temp.txt
  ```

* 删除HDFS下的文档

  ```
  bin/hadoop fs -rmr ./in/test1.txt
  ```

* 查看HDFS基本统计信息

  ```
  bin/hadoop dfsadmin -report
  ```

* 查看HDFS下某个文件的内容

  ```
  bin/hadoop fs -ls ./in
  ```

### 怎样添加节点

1. 在新节点安装好 Hadoop
2. 把NameNode的有关配置文件复制到该节点
3. 修改 masters和 slaves文件,增加该节点
4. 设置ssh免密码进出该节点
5. 单独启动该节点上的 Datanode和 Tasktracker（hadoop-daemon.sh start datanode/tasktracker）
6. 运行 start- balancer. sh进行数据负载均衡
7. 不用重启集群

---

### API方式（Java ）

**设置Hapoop的类目录**

```
#conf/hadoop-env.sh文件

export HADOOP_CLASSPATH=/home/duxiaoming/hadoop-1.1.2/myclass
```

**设置搜索目录**

> 使得运行javac，jpc等程序时，省去打入一长串路径

**安装gcc**

> yum -y install gcc gcc-c++ autoconf make

#### 例子：

```
import java.io.InputStream;
import java.net.URL;
import org.apache.hadoop.fs.FsUrlStreamHandlerFactory;
import org.apache.hadoop.io.IOUtils;

public class DFSClient {

    static {
        URL.setURLStreamHandlerFactory(new FsUrlStreamHandlerFactory());
    }

    public static void main(String[] args) throws Exception {
        InputStream in = null;
        try {
            in = new URL(args[0]).openStream();
            IOUtils.copyBytes(in, System.out, 4096, false);
        } finally {
            // TODO: handle finally clause
            IOUtils.closeStream(in);
        }
    }

}
```

加上hadoop-core-1.1.2.jar 目录进行编译

```
javac -classpath ../hadoop-core-1.1.2.jar DFSClient.java
```

启动

```
../bin/hadoop DFSClient hdfs://backup01:9000/user/duxiaoming/in/test1.txt
```

显示

```
hello world
```

### 使用ANT来编译

**build.xml**

```
<project name="HDFSJavaAPI" default="compile" basedir=".">
    <property name="build" location="build" />
    <property environment="env"/>

    <path id="hadoop-classpath">
        <fileset dir="${env.HADOOP_HOME}/lib">
            <include name="**/*.jar" />
        </fileset>
        <fileset dir="${env.HADOOP_HOME}">
            <include name="**/*.jar" />
        </fileset>
    </path>

    <target name="compile">
        <mkdir dir="${build}" />
        <javac includeantruntime="false" srcdir="src" destdir="${build}">
            <classpath refid="hadoop-classpath"/>
        </javac>
        <jar jarfile="HDFSJavaAPI.jar" basedir="${build}" />
    </target>        

    <target name="clean">
        <delete dir="${build}" />
    </target>

    <target name="print-cp">
        <property name="classpath" refid="hadoop-classpath"/>
        <echo message="classpath= ${classpath}"/>
    </target>
</project>
```

**HDFSJavaAPIDemo.java**

```
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;


public class HDFSJavaAPIDemo {

    public static void main(String[] args) throws IOException {
        Configuration conf = new Configuration();
        conf.addResource(new Path(
                "/home/duxiaoming/hadoop-1.1.2/conf/core-site.xml"));
        conf.addResource(new Path(
                "/home/duxiaoming/hadoop-1.1.2/conf/hdfs-site.xml"));

        FileSystem fileSystem = FileSystem.get(conf);
        System.out.println(fileSystem.getUri());

        Path file = new Path("demo.txt");
        if (fileSystem.exists(file)) {
            System.out.println("File exists.");
        } else {
            // Writing to file
            FSDataOutputStream outStream = fileSystem.create(file);
            outStream.writeUTF("Welcome to HDFS Java API!!!");
            outStream.close();
        }

        // Reading from file
        FSDataInputStream inStream = fileSystem.open(file);
        String data = inStream.readUTF();
        System.out.println(data);
        inStream.close();

        // deleting the file. Non-recursively.
        // fileSystem.delete(file, false);

        fileSystem.close();
    }
}
```

**生成jar包：**

```
/usr/local/apache-ant-1.9.2/bin/ant
```

**运行：**

```
/home/duxiaoming/hadoop-1.1.2/bin/hadoop jar HDFSJavaAPI.jar HDFSJavaAPIDemo
```



