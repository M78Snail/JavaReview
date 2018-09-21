## **Struts2入门配置**

#### Ⅰ配置Struts2的前端控制器（web.xml）

```css
<filter>
	<filter-name>struts2</filter-name>
	<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>struts2</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

#### Ⅱ.**编写Struts的配置文件（struts.xml）**

1. 配置文件名称是struts.xml（名称必须是struts.xml）

2. 在src下引入struts.xml配置文件（配置文件的路径必须是在src的目录下）

3. 配置如下：

   ```
   <?xml version="1.0" encoding="UTF-8" ?>
       <!DOCTYPE struts PUBLIC
           "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
           "http://struts.apache.org/dtds/struts-2.3.dtd">
   <struts>
   	<package name="default" namespace="/" extends="struts-default">
   		<action name="hello" class="com.itheima.action.HelloAction"
   			method="sayHello">
   		</action>
   	</package>
   </struts>
   ```

#### Ⅲ.**编写Action类**

```
package com.itheima.action;

public class HelloAction {
	public String sayHello() {
		System.out.println("Hello Struts2!!");
		return null;
	}
}
```

------

#### **Struts2的执行流程**

```
1. 执行的流程
    * 编写的页面，点击超链接，请求提交到服务器端。
    * 请求会先经过Struts2的核心过滤器（StrutsPrepareAndExecuteFilter）
        * 过滤器的功能是完成了一部分代码功能
        * 就是一系列的拦截器执行了，进行一些处理工作。
        * 咱们可以在struts-default.xml配置文件中看到有很多的拦截器。可以通过断点的方式来演示。
        * 拦截器执行完后，会根据struts.xml的配置文件找到请求路径，找到具体的类，通过反射的方式让方法执行。

2. 总结
    * JSP页面-->StrutsPrepereAndExecuteFilter过滤器-->执行一系列拦截器（完成了部分代码）-->执行到目标Action-->返回字符串-->结果页面（result）-->页面跳转
```

------

#### **Action类的三种写法**

* Action类就是一个POJO类
    *  什么是POJO类，POJO（Plain Ordinary Java Object）简单的Java对象.简单记：没有继承某个类，没有实现接口，就是POJO的类。
* Action类可以实现Action接口
    * Action接口中定义了5个常量，5个常量的值对应的是5个逻辑视图跳转页面（跳转的页面还是需要自己来配置），还定义了一个方法，execute方法。
        * 大家需要掌握5个逻辑视图的常量
            * SUCCESS       -- 成功.
            * INPUT         -- 用于数据表单校验.如果校验失败,跳转INPUT视图.
            * LOGIN         -- 登录.
            * ERROR         -- 错误.
            * NONE          -- 页面不转向.
* Action类可以去继承ActionSupport类（开发中这种方式使用最多）
        * 设置错误信息

------

#### **Struts2配置常量**

1. 可以在Struts2框架中的哪些配置文件中配置常量？
    * struts.xml（必须要掌握，开发中基本上就在该配置文件中编写常量）
        * <constant name="key" value="value"></constant>
    * web.xml
        * 在StrutsPrepareAndExecuteFilter配置文件中配置初始化参数
    * 注意：后加载的配置的文件的常量会覆盖之前加载的常量！！

2. 需要大家了解的常量
    * struts.i18n.encoding=UTF-8            -- 指定默认编码集,作用于HttpServletRequest的setCharacterEncoding方法 
    * struts.action.extension=action,,      -- 该属性指定需要Struts 2处理的请求后缀，该属性的默认值是action，即所有匹配*.action的请求都由Struts2处理。如果用户需要指定多个请求后缀，则多个后缀之间以英文逗号（,）隔开
    * struts.serve.static.browserCache=true     -- 设置浏览器是否缓存静态内容,默认值为true(生产环境下使用),开发阶段最好关闭 
    * struts.configuration.xml.reload=false     -- 当struts的配置文件修改后,系统是否自动重新加载该文件,默认值为false(生产环境下使用) 
    * struts.devMode = false                    -- 开发模式下使用,这样可以打印出更详细的错误信息 