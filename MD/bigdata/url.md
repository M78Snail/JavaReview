# 10万个URL去重

问题:有10 亿个 url，每个 url 大小小于 56B，要求去重，内存只给你4G

思路：

1.首先将给定的url调用hash方法计算出对应的hash的value，在10亿的url中相同url必然有着相同的value。

2.对每个url求取hash(url)%1000，然后根据所取得的值将url分别存储到1000个小文件

3.在每个文件上将hash(url)%1000值作为key，url值作为value构成hash表进行去重。

4.将内存中去重后的hash表中的value值即url写入磁盘，合并磁盘中的各部分url文件，完成去重。