## 显示的配置 处理器映射器和处理器适配器

---

原因：如果没有显示的配置 处理器映射器和处理器适配器，那么springMvc会去默认的dispatcherServlet.properties中查找，对应的处理器映射器和处理器适配器去使用，这样每个请求都要扫描一次他的默认配置文件，效率非常低，会降低访问速度。

### 注解驱动

```
<!-- 自动注册DefaultAnootationHandlerMapping,AnotationMethodHandlerAdapter -->
<mvc:annotation-driven></mvc:annotation-driven>
```

### 视图解析器

```
<!-- 配置视图解析器 -->
<bean
     class="org.springframework.web.servlet.view.InternalResourceViewResolver">
     <property name="prefix" value="/WEB-INF/jsp/"></property>
     <property name="suffix" value=".jsp"></property>
</bean>
```

配置之前：

```
// 指定返回的页面位置
modelAndView.setViewName("/WEB-INF/jsp/itemList.jsp");
```

配置之后

```
// 不用写完整名称
modelAndView.setViewName("itemList");
```



