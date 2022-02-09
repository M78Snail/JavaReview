## 配置

---

**web.xml**

1.设置核心分发器

```css
<servlet>
	<servlet-name>springMvc</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet.class</servlet-class>
</servlet>
<servlet-mapping>
	<servlet-name>springMvc</servlet-name>
	<url-pattern>*.action</url-pattern>
</servlet-mapping>
```

2.设置核心配置文件

```
<init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc/SpringMvc.xml</param-value>
</init-param>
```

3.配置扫描所有使用注解的类型

```
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/mvc
	http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">
	
	<!-- 配置@Controller注解 -->
	<context:component-scan base-package="com.test.controller"></context:component-scan>
	
</beans>
```

4.配置@Controller和模型与视图

```
@Controller
public class ItemsController {

	@RequestMapping("/list")
	public ModelAndView itemList() throws Exception {
		List<Items> itemList = new ArrayList<>();

		// 商品列表
		Items items_1 = new Items();
		items_1.setName("联想笔记本_3");
		items_1.setPrice(6000f);
		items_1.setDetail("ThinkPad T430 联想笔记本电脑！");

		Items items_2 = new Items();
		items_2.setName("苹果手机");
		items_2.setPrice(5000f);
		items_2.setDetail("iphone6苹果手机！");

		itemList.add(items_1);
		itemList.add(items_2);

		// 模型和视图
		// model模型: 模型对象中存放了返回给页面的数据
		// view视图: 视图对象中指定了返回的页面的位置
		ModelAndView modelAndView = new ModelAndView();

		// 将返回给页面的数据放入模型和视图对象中
		modelAndView.addObject("itemList", itemList);

		// 指定返回的页面位置
		modelAndView.setViewName("/WEB-INF/jsp/itemList.jsp");

		return modelAndView;
	}

}
```

![](https://github.com/M78Snail/JavaReview/blob/master/MD/springmvc/assets/importspringmvc1.png)
