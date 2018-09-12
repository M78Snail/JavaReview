## 定义upload.properties文件

```java
#image url
IMAGE_SERVER_URL=http://192.168.25.133/
```

## 添加到SpringMvc容器中

```java
<context:property-placeholder location="classpath:resource/upload.properties"/>
```

## 在Controller中获取

```java
@Value("${IMAGE_SERVER_URL}")
private String IMAGE_SERVER_URL;
```



