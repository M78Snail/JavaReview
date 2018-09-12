## 拦截器:

> 作用:拦截请求,一般做登录权限验证时用的比较多

### 1\)需要编写自定义拦截器类,实现HandlerInterceptor接口

```java
public class Interceptor1 implements HandlerInterceptor {

    //执行时机:controller已经执行,modelAndview已经返回
    //使用场景: 记录操作日志,记录登录用户的ip,时间等.
    @Override
    public void afterCompletion(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, Exception arg3)
            throws Exception {
        System.out.println("======Interceptor1=======afterCompletion========");
    }

    //执行时机:Controller方法已经执行,ModelAndView没有返回
    //使用场景: 可以在此方法中设置全局的数据处理业务
    @Override
    public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, ModelAndView arg3)
            throws Exception {
        System.out.println("======Interceptor1=======postHandle========");

    }

    //返回布尔值:如果返回true放行,返回false则被拦截住
    //执行时机:controller方法没有被执行,ModelAndView没有被返回
    //使用场景: 权限验证
    @Override
    public boolean preHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2) throws Exception {
        System.out.println("======Interceptor1=======preHandle========");
        return true;
    }

}
```

### 2\)在spirngMvc.xml中配置拦截器生效

```java
<!-- 配置拦截器 -->
<mvc:interceptors>
    <!-- 多个拦截器的执行顺序等于springMvc.xml中的配置顺序 -->
    <!-- <mvc:interceptor> -->
    <!-- 拦截请求的路径 要拦截所有必需配置成/** -->
    <!-- <mvc:mapping path="/**"/> -->
    <!-- 指定拦截器的位置 -->
    <!-- <bean class="cn.itheima.interceptor.Interceptor1"></bean> -->
    <!-- </mvc:interceptor> -->

    <mvc:interceptor>
        <!-- 拦截请求的路径 要拦截所有必需配置成/** -->
        <mvc:mapping path="/**" />
        <!-- 指定拦截器的位置 -->
        <bean class="com.zjipst.interceptor.Interceptor1"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

## 



