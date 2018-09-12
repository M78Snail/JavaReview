### 窄化请求映射

> 类名也可以加@RequestMapping 为防止你和你的队友在conroller方法起名的时候重名,所以相当于在url中多加了一层目录,防止重名

```java
@Controller

@RequestMapping("/items")
public class ItemsController {
    ....
｝
```

### 限制请求方式

```java
@RequestMapping(value="/list", method=RequestMethod.GET)
```



