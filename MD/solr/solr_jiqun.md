## ![](/assets/importsolr-cloud.png)

## 创建三个Zookeeper节点

1. 创建三个Zookeeper目录
   ```
   cp -r zookeeper-3.4.6 /usr/local/solr-cloud/zookeeper01
   cp -r zookeeper-3.4.6 /usr/local/solr-cloud/zookeeper02
   cp -r zookeeper-3.4.6 /usr/local/solr-cloud/zookeeper03
   ```
2. zookeeper解压后修改新建data目录，并创建myid,写入1（其他两个依次写2，3）
   ```
   echo 1 > data/myid
   ```
3. 修改conf目录下的zoo.cfg

   1. zoo.cfg的dataDir

      ```
      dataDir=/usr/local/solr-cloud/zookeeper01/data
      ```

   2. clientPort=2181

      ```
      三个clientPort不能相同
      ```

   3. 添加server.1,server.2,server.3

      ```
      server.1=192.168.25.130:2881:3881
      server.2=192.168.25.130:2882:3882
      server.3=192.168.25.130:2882:3883
      ```

4. 批处理启动zookeeper

   创建start-zookeeper.sh

   ```
   vim start-zookeeper.sh
   ```

   批处理

   ```
   cd zookeeper01/bin
   ./zkServer.sh start
   cd ../../
   cd zookeeper02/bin
   ./zkServer.sh start
   cd ../../
   cd zookeeper03/bin
   ./zkServer.sh start
   cd ../../
   ```

   加权限并启动

   ```
   chmod u+x start-zookeeper.sh
   ./start-zookeeper.sh
   ```

---

## 配置Solr集群

1. 复制四个tomcat到solr-cloud下面
   ```
   cp -r apache-tomcat-7.0.47 /usr/local/solr-cloud/tomcat01
   cp -r apache-tomcat-7.0.47 /usr/local/solr-cloud/tomcat02
   cp -r apache-tomcat-7.0.47 /usr/local/solr-cloud/tomcat03
   cp -r apache-tomcat-7.0.47 /usr/local/solr-cloud/tomcat04
   ```
2. 修改四个tomcat下面的conf/server.xml 的三个port端口号，保证四个tomcat不一样

   ```
   <Server port="8405" shutdown="SHUTDOWN">

   <Connector port="8480" protocol="HTTP/1.1"
                  connectionTimeout="20000"
                  redirectPort="8443" />

   <Connector port="8409" protocol="AJP/1.3" redirectPort="8443" />
   ```

3. 将单机版的tomcat下面的solr工程复制到集群4个tomcat下面

   ```
   cp -r /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/ /usr/local/solr-cloud/tomcat01/webapps/
   cp -r /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/ /usr/local/solr-cloud/tomcat02/webapps/
   cp -r /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/ /usr/local/solr-cloud/tomcat03/webapps/
   cp -r /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/ /usr/local/solr-cloud/tomcat04/webapps/
   ```

4. 将单机版的solr下面的solrhome复制到集群solr-cloud

   ```
   cp -r /usr/local/solr/solrhome/ /usr/local/solr-cloud/solrhome01
   cp -r /usr/local/solr/solrhome/ /usr/local/solr-cloud/solrhome02
   cp -r /usr/local/solr/solrhome/ /usr/local/solr-cloud/solrhome03
   cp -r /usr/local/solr/solrhome/ /usr/local/solr-cloud/solrhome04
   ```

5. 修改每个solrhome下面的solr.xml

   ```
   <str name="host">192.168.25.130</str>
   <int name="hostPort">8180</int>
   ```

6. 修改每个tomcat的web配置文件的solrhome位置

   ```
   <env-entry>
        <env-entry-name>solr/home</env-entry-name>
        <env-entry-value>/usr/local/solr-cloud/solrhome01</env-entry-value>
        <env-entry-type>java.lang.String</env-entry-type>
   </env-entry>
   ```

7. 修改每个tomcat/bin下的catalina.sh文件，关联solr和zookeeper。添加下面这行

   ```
   JAVA_OPTS="-DzkHost=192.168.25.130:2181,192.168.25.130:2182,192.168.25.130:2183"
   ```

8. 上传solrhome的配置文件上传到zookeeper，使用solr的/example/scripts/cloud-scripts目录下的zkcli.sh

   ```
   ./zkcli.sh -zkhost 192.168.25.130:2181,192.168.25.130:2182,192.168.25.130:2183 -cmd upconfig -confdir /usr/local/solr-cloud/solrhome01/collection1/conf/ -confname myconf
   ```

9. 连接zookeeper的命令

   ```
   cd /usr/local/solr-cloud/zookeeper01/bin
   ./zkCli.sh -server 192.168.25.130:2182
   ```

10. 启动所有tomcat，编写批处理

    ```
    /usr/local/solr-cloud/tomcat01/bin/startup.sh
    /usr/local/solr-cloud/tomcat02/bin/startup.sh
    /usr/local/solr-cloud/tomcat03/bin/startup.sh
    /usr/local/solr-cloud/tomcat04/bin/startup.sh
    ```

11. 加权限

    ```
    chmod +x start-tomcat.sh 
    ```

12. 查看tomcat启动信息

    ```
    tail -f /usr/local/solr-cloud/tomcat01/logs/catalina.out 
    ```

---

## 分片

1. SolrCloud创建Collection的命令

   ```java
   http://192.168.25.130:8180/solr/admin/collections?action=CREATE&name=collection2&numShards=2&replicationFactor=2
   ```

2. SolrCloud删除Collection的命令

   ```
   http://192.168.25.130:8180/solr/admin/collections?action=DELETE&name=collection1
   ```



