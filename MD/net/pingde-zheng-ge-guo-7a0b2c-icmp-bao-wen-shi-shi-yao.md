## Ping的整个过程。ICMP报文是什么 {#toc_28}

在了解ping命令之前，我们首先需要了解一下ICMP协议，即：网络控制消息协议（Internet Control Message Protocol）。  
　　ICMP是TCP/IP协议族的一个子协议，工作在网络互联层（网络层）。ICMP协议是一种面向无连接的协议，用于传输出错报告控制信息。用于在IP主机、路由器之间传递控制消息。控制消息是指网络通不通、主机是否可达、路由是否可用等网络本身的消息。这些控制消息虽然并不传输用户数据，但是对于用户数据的传递起着重要的作用。

### ping某个域名的整个过程 {#toc_29}

ICMP的一个重要应用就是分组网间探测PING（Packe InterNet Groper），用来测试主机之间的连通性。PING使用了ICMP回送请求与回送回答报文。PING是应用层直接使用网络层ICMP的一个例子，没有经过传输层的TCP或UDP。

| ![](/assets/import7.11.png) |
| :---: |

