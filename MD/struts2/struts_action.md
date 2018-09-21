## **Action的三种访问**

1. 通过<action>标签中的method属性，访问到Action中的具体的方法。
    * 传统的配置方式，配置更清晰更好理解！但是扩展需要修改配置文件等！
    * 具体的实例如下：
        * 页面代码
            ```
            <a href="${pageContext.request.contextPath}/addBook.action">添加图书</a>
            ```

            ```
            <a href="${pageContext.request.contextPath}/deleteBook.action">删除图书</a>
            ```

        * 配置文件的代码

            ```
            <package name="demo2" extends="struts-default" namespace="/">
                <action name="addBook" class="cn.itcast.demo2.BookAction" method="add">		</action>
                <action name="deleteBook" class="cn.itcast.demo2.BookAction" method="delete">
                </action>
            </package>
            ```

        * Action的代码

            ```
            public String add(){
                System.out.println("添加图书");
                return NONE;
            }
            public String delete(){
                System.out.println("删除图书");
                return NONE;
            }
            ```
2. 通配符的访问方式:(访问的路径和方法的名称必须要有某种联系.)  通配符就是 * 代表任意的字符
    * 使用通配符的方式可以简化配置文件的代码编写，而且扩展和维护比较容易。
    * 具体实例如下：
        * 页面代码

            ```
            <a href="${pageContext.request.contextPath}/order_add.action">添加订单</a>
            ```

            ```
            <a href="${pageContext.request.contextPath}/order_delete.action">删除订单</a>
            ```

        * 配置文件代码

            ```
            <action name="order_*" class="cn.itcast.demo2.OrderAction" method="{1}"></action>
            ```

        * Action的代码

            ```
            public String add(){
                System.out.println("添加订单");
                return NONE;
            }
            public String delete(){
                System.out.println("删除订单");
                return NONE;
            }
            ```

    * 具体理解：在JSP页面发送请求，http://localhost/struts2_01/order_add.action，配置文件中的order_*可以匹配该请求，*就相当于变成了add，method属性的值使用{1}来代替，{1}就表示的是第一个*号的位置！！所以method的值就等于了add，那么就找到Action类中的add方法，那么add方法就执行了！

3. 动态方法访问的方式（有的开发中也会使用这种方式）
    * 如果想完成动态方法访问的方式，需要开启一个常量，**struts.enable.DynamicMethodInvocation = false**，把值设置成true。
        * 注意：不同的Struts2框架的版本，该常量的值不一定是true或者false，需要自己来看一下。如果是false，需要自己开启。

        * 在struts.xml中开启该常量。

            ```
            <constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>
            ```

    * 具体代码如如下
        * 页面的代码
            ```
            <a href="${pageContext.request.contextPath}/product!add.action">添加商品</a>
            ```

            ```
            <a href="${pageContext.request.contextPath}/product!delete.action">删除商品</a>
            ```

        * 配置文件代码

            ```
            <action name="product" class="cn.itcast.demo2.ProductAction"></action>
            ```

        * Action的类的代码

            ```
            public class ProductAction extends ActionSupport{
                public String add(){
                    System.out.println("添加订单");
                    return NONE;
                }
                public String delete(){
                    System.out.println("删除订单");
                    return NONE;
                }
            }
            ```
