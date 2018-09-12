## Json数据交互:

### 1.需要加入jackson的jar包

如果使用&lt;mvc:annotation-driven /&gt; 则配置文件不用改。

### 2.@Requestbody:将页面传到controller中的json格式字符串自动转换成java的pojo对象

> 作用：  
> @RequestBody注解用于读取http请求的内容\(字符串\)，通过springmvc提供的HttpMessageConverter接口将读到的内容转换为json、xml等格式的数据并绑定到controller方法的参数上。

```java
@RequestMapping("/sendJson")
@ResponseBody
public Items sendJson(@RequestBody Items items) throws Exception{
    System.out.println(items);
    return items;
}
```

### 3.@ResponseBody:将java中pojo对象自动转换成json格式字符串返回给页面

> 作用：  
> 该注解用于将Controller的方法返回的对象，通过HttpMessageConverter接口转换为指定格式的数据如：json,xml等，通过Response响应给客户端

```java
@RequestMapping("/sendJson")
@ResponseBody
public Items sendJson(@RequestBody Items items) throws Exception{
    System.out.println(items);
    return items;
}
```



