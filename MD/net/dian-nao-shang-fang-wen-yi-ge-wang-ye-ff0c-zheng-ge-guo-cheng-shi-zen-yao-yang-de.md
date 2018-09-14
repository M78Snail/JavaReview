## 电脑上访问一个网页，整个过程是怎么样的 {#toc_23}

### &lt;1&gt;连接 {#toc_24}

　　当我们输入这样一个请求时，首先要建立一个socket连接，因为socket是通过ip和端口建立的，所以之前还有一个DNS解析过程，把http://www.mytest.com/变成ip，如果url里不包含端口号，则会使用该协议的默认端口号。DNS的过程是这样的：首先我们知道我们本地的机器上在配置网络时都会填写DNS，这样本机就会把这个url发给这个配置的DNS服务器，如果能够找到相应的url则返回其ip，否则该DNS将继续将该解析请求发送给上级DNS，整个DNS可以看做是一个树状结构，该请求将一直发送到根直到得到结果。现在已经拥有了目标ip和端口号，这样我们就可以打开socket连接了。

### &lt;2&gt;请求 {#toc_25}

　　连接成功建立后，开始向web服务器发送请求，这个请求一般是GET或POST命令（POST用于FORM参数的传递）。GET命令的格式为：　　GET 路径/文件名 HTTP/1.0 文件名指出所访问的文件，HTTP/1.0指出Web浏览器使用的HTTP版本。现在可以发送GET命令：

```
GET /mytest/index.html HTTP/1.0
```

### &lt;3&gt;应答 {#toc_26}

　　Web服务器收到这个请求，进行处理。从它的文档空间中搜索子目录mytest的文件index.html。如果找到该文件，Web服务器把该文件内容传送给相应的Web浏览器。  
　　为了告知浏览器，，Web服务器首先传送一些HTTP头信息，然后传送具体内容（即HTTP体信息），HTTP头信息和HTTP体信息之间用一个空行分开。  
　　常用的HTTP头信息有：  
　　　① HTTP 1.0 200 OK 　这是Web服务器应答的第一行，列出服务器正在运行的HTTP版本号和应答代码。代码”200 OK”表示请求完成。  
　　　② MIME\_Version:1.0　它指示MIME类型的版本。  
　　　③ content\_type:类型　这个头信息非常重要，它指示HTTP体信息的MIME类型。如：content\_type:text/html指示传送的数据是HTML文档。  
　　　④ content\_length:长度值　它指示HTTP体信息的长度（字节）。

### &lt;4&gt;关闭连接 {#toc_27}

　　当应答结束后，Web浏览器与Web服务器必须断开，以保证其它Web浏览器能够与Web服务器建立连接。

