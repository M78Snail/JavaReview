## **自定义拦截器和配置**

1. 编写拦截器，需要实现Interceptor接口，实现接口中的三个方法

    ```
    protected String doIntercept(ActionInvocation invocation) throws Exception {
        // 获取session对象
        User user = (User) ServletActionContext.getRequest().getSession().getAttribute("existUser");
        if(user == null){
            // 说明，没有登录，后面就不会执行了
            return "login";
        }
        return invocation.invoke();
    }
    ```

2. 需要在struts.xml中进行拦截器的配置，配置一共有两种方式

    第一种方式：定义了拦截器

    ```
    <!--  -->
    <interceptors>
        <interceptor name="DemoInterceptor" class="com.itheima.interceptor.DemoInterceptor"/>
    </interceptors>
    ```

    第二种方式：定义拦截器栈

    ```
    <interceptors>
        <interceptor name="DemoInterceptor" class="com.itheima.interceptor.DemoInterceptor"/>
        <!-- 定义拦截器栈 -->
        <interceptor-stack name="myStack">
            <interceptor-ref name="DemoInterceptor"/>
            <interceptor-ref name="defaultStack"/>
        </interceptor-stack>
    </interceptors>
    ```


## 配置

    <action name="userAction" class="com.itheima.demo3.UserAction">
        <!-- 只要是引用自己的拦截器，默认栈的拦截器就不执行了，必须要手动引入默认栈 
        <interceptor-ref name="DemoInterceptor"/>
        <interceptor-ref name="defaultStack"/>
        -->
    
        <!-- 引入拦截器栈就OK -->
        <interceptor-ref name="myStack"/>
    </action>