## TCP与UDP的区别 {#toc_3}

下面我们主要从连接性(Connectivity)、可靠性(Reliability)、有序性(Ordering)、有界性(Boundary)、拥塞控制(Congestion or Flow control)、传输速度(Speed)、量级(Heavy/Light weight)、头部大小(Header size)等8个方面来对比它们：

1. TCP是面向连接(Connection oriented)的协议，UDP是无连接(Connection less)协议；
   TCP用三次握手建立连接：1) Client向server发送SYN；2) Server接收到SYN，回复Client一个SYN-ACK；3) Client接收到SYN_ACK，回复Server一个ACK。到此，连接建成。UDP发送数据前不需要建立连接。
2. TCP可靠，UDP不可靠；TCP丢包会自动重传，UDP不会。
3. TCP有序，UDP无序；消息在传输过程中可能会乱序，后发送的消息可能会先到达，TCP会对其进行重排序，UDP不会。
4. TCP无界，UDP有界；TCP通过字节流传输，UDP中每一个包都是单独的。
5. TCP有流量控制（拥塞控制），UDP没有；主要靠三次握手实现。
6. TCP传输慢，UDP传输快；因为TCP需要建立连接、保证可靠性和有序性，所以比较耗时。这就是为什么视频流、广播电视、在线多媒体游戏等选择使用UDP。
7. TCP是重量级的，UDP是轻量级的；TCP要建立连接、保证可靠性和有序性，就会传输更多的信息，如TCP的包头比较大。
8. TCP的头部比UDP大；TCP头部需要20字节，UDP头部只要8个字节

  


