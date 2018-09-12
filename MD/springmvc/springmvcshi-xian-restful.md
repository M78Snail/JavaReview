### 1.什么是restful？

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格，是对http协议的诠释。

* 资源定位：互联网所有的事物都是资源，要求url中没有动词，只有名词。没有参数  。

* Url格式：http://blog.csdn.net/beat\_the\_world/article/details/45621673

* 资源操作：使用put、delete、post、get，使用不同方法对资源进行操作。分别对应添加、删除、修改、查询。一般使用时还是post和get。Put和Delete几乎不使用。

### 2 .  URL 模板模式映射

* @RequestMapping\(value="/ viewItems/{id}"\)：{×××}占位符，请求的URL可以是“/viewItems/1”或“/viewItems/2”，

* @PathVariable获取{×××}中的×××变量。  @PathVariable用于将请求URL中的模板变量映射到功能处理方法的参数上。

```java
@RequestMapping("/viewItems/{id}") 
public @ResponseBody viewItems(@PathVariable("id") String id,Model model) throws Exception{
	//方法中使用@PathVariable获取useried的值，使用model传回页面
	//调用 service查询商品信息
	ItemsCustom itemsCustom = itemsService.findItemsById(id);
	return itemsCustom;
}
```



