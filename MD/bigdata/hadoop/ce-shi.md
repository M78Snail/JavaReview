## Hadoop1.1.2

### 测试两个文本的单词数量

* 创建两个文本

  ```
  cd /home/duxiaoming
  mkdir input
  cd input/
  echo "hello world" > test1.txt
  echo "hello hadoop" > test2.txt
  ```

* 拷贝到Hadoop文件系统

  ```
  cd hadoop-1.1.2
  bin/hadoop fs -put ../input ./in                 // ./ 默认代表/user/duxiaoming/
  bin/hadoop fs -ls ./in/*             //查看详情
  ```

* 运行jar包

  ```
  bin/hadoop jar hadoop-examples-1.1.2.jar wordcount in out         //分析in目录下的文件，输出到out目录
  ```

* 查看生成的文件

  ```
  /user/duxiaoming/out/_SUCCESS
  /user/duxiaoming/out/_logs
  /user/duxiaoming/out/part-r-0000

  bin/hadoop fs -cat ./out/part-r-0000
  ```

### 通过web了解Hadoop的活动

* 通过浏览器和http访问jobtracker所在的节点的50030端口监控jobtracker
* 通过浏览器和http访问namenode所在的节点的50070端口监控集群



