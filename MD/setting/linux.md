## 1.安装jdk

```
a.检测是否安装了jdk  运行java -version

b.若有需要将其卸载

c.查看安装那些jdk

    rpm -qa | grep java

d.卸载

    先卸载 openjdk 1.7

         rpm -e --nodeps 卸载的包

         rpm -e --nodeps java-1.7.0-openjdk-1.7.0.45-2.4.3.3.el6.i686

    再卸载 openjdk 1.6        

        rpm -e --nodeps java-1.6.0-openjdk-1.6.0.0-1.66.1.13.0.el6.i686

e.安装jdk

    上传jdk 通过ftp软件上传(上传到root目录下\)

    在 /usr/local创建一个 java目录

        mkdir java

    将上传的jdk复制到 java目录下

        cp /root/jdk.xxxxx.tar /usr/local/java

    将其解压

        tar -xvf jdk.xxx.tar

f.安装依赖

    yum install glibc.i686

g.配置环境变量

    编辑  vi /etc/profile

    在文件最后添加一下信息

        #set java environment

        JAVA_HOME=/usr/local/java/jdk1.7.0_55

        CLASSPATH=.:$JAVA_HOME/lib.tools.jar

        PATH=$JAVA_HOME/bin:$PATH

        export JAVA_HOME CLASSPATH PATH

    保存退出

    source /etc/profile  使更改的配置立即生效
```

## 2.安装mysql

```
a.检测是否安装了mysql

    rpm  -qa \| grep mysql

b.卸载系统自带的mysql

    rpm -e --nodeps 卸载的包

    rpm -e --nodeps mysql-libs-5.1.71-1.el6.i686 

c.上传mysql

d.在 /usr/local/ 创建一个mysql

e.复制mysql 到 mysql目录下

f.解压 tar

    会有几个rpm文件

g.安装

    安装mysql的服务器端

        rpm -ivh MySQL-server-5.5.49-1.linux2.6.i386.rpm

        注意:第一次登录mysql的时候没有不需要密码的 以后都需要

    安装mysql的客户端

        rpm -ivh MySQL-client-5.5.49-1.linux2.6.i386.rpm

h.查看mysql的服务状态

    service mysql status 

  启动 mysql

    service mysql start

  停止mysql

    service mysql stop



i.修改mysql的root的密码

    登录:mysql -uroot

    修改密码:

        use mysql;

        update user set password = password\('1234'\) where user = 'root';

        flush privileges;\# 刷新

j.开启远程访问

    grant all privileges on \*.\* to 'root' @'%' identified by '1234';

    flush privileges;

k.开启防火墙端口 3306 退出mysql

    3306端口放行 

    /sbin/iptables -I INPUT -p tcp --dport 3306 -j ACCEPT

    将该设置添加到防火墙的规则中

    /etc/rc.d/init.d/iptables save

l:设置mysql的服务随着系统的启动而启动

    加入到系统服务：

    chkconfig --add mysql

    自动启动：

    chkconfig mysql on
```

## 3.安装tomcat

```
a.在/usr/local/        创建tomcat目录

b.复制tomcat 到 /usr/local/tomcat

c.解压tomcat

d.启动tomcat 进入 bin

    方式1:

        sh startup.sh

    方式2:

        ./startup.sh

e.开启端口号 8080

    8080端口放行 

    /sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT

    将该设置添加到防火墙的规则中

    /etc/rc.d/init.d/iptables save



注意:

    查看日志文件

        tail -f logs/catalina.out

    退出 ctrl+c
```



