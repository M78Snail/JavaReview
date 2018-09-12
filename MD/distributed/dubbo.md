## zookeeper的安装

1.zookeeper解压后修改conf目录下的zoo.cfg的dataDir（自己新建）

```java
# example sakes.
dataDir=/usr/local/zookeeper/zookeeper-3.4.6/data
# the port at which the clients will connect
```

2.在 /etc/sysconfig/iptables中添加下边内容：

```java
-A INPUT -m state --state NEW -m tcp -p tcp --dport 20880 -j ACCEPT
```

表示开启20880端口

3.然后:

```java
service iptables restart
```

重启防火墙即可。

或者关闭防火墙：

```
service iptables stop
```

## 注册服务

```java
<!-- 发布dubbo服务 -->
<!-- 提供方应用信息，用于计算依赖关系 -->
<dubbo:application name="taotao-manager" />
<!-- 注册中心的地址 -->
<dubbo:registry protocol="zookeeper" address="192.168.40.130:2181" />
<!-- 用dubbo协议在20880端口暴露服务 -->
<dubbo:protocol name="dubbo" port="20880" />
<!-- 声明需要暴露的服务接口 -->
<dubbo:service interface="com.taotao.service.ItemService" ref="itemServiceImpl" timeout="300000" />
```

## 调用服务

```java
<!-- 引用dubbo服务 -->
<dubbo:application name="taotao-manager-web" />
<dubbo:registry protocol="zookeeper" address="192.168.40.130:2181" />
<dubbo:reference interface="com.taotao.service.ItemService" id="itemService" />
```



