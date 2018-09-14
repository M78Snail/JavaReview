## Http的报文结构 {#toc_18}

HTTP请求报文由3部分组成\(请求行+请求头+请求体\)

| ![](/assets/import7.1.1.png) | ![](/assets/import7.1.2.png) |
| :---: | :---: |


### 请求例子 {#toc_19}

```java
POST　　/meme.php/login　　HTTP/1.1
HOST:114.215.86.90
Cache-Control:no-cache

tel=13637 & password=123456
```

### 响应例子 {#toc_20}

```java
HTTP/1.1　　200　　OK
Date:Sat

{"status":202}
```



