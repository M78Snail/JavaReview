# Java中session的用法

## 一、Session简单介绍

　　在WEB开发中，服务器可以为每个用户浏览器创建一个会话对象（session对象），注意：一个浏览器独占一个session对象(默认情况下)。因此，在需要保存用户数据时，服务器程序可以把用户数据写到用户浏览器独占的session中，当用户使用浏览器访问其它程序时，其它程序可以从用户的session中取出该用户的数据，为用户服务。

## 二、Session和Cookie的主要区别

- Cookie是把用户的数据写给用户的浏览器。
- Session技术把用户的数据写到用户独占的session中。
- Session对象由服务器创建，开发人员可以调用request对象的getSession方法得到session对象。

## 三、session实现原理

### 3.1、服务器是如何实现一个session为一个用户浏览器服务的？

　　服务器创建session出来后，会把session的id号，以cookie的形式回写给客户机，这样，只要客户机的浏览器不关，再去访问服务器时，都会带着session的id号去，服务器发现客户机浏览器带session id过来了，就会使用内存中与之对应的session为之服务。可以用如下的代码证明：

```text
package xdp.gacl.session;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

public class SessionDemo1 extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setCharacterEncoding("UTF=8");
        response.setContentType("text/html;charset=UTF-8");
        //使用request对象的getSession()获取session，如果session不存在则创建一个
        HttpSession session = request.getSession();
        //将数据存储到session中
        session.setAttribute("data", "孤傲苍狼");
        //获取session的Id
        String sessionId = session.getId();
        //判断session是不是新创建的
        if (session.isNew()) {
            response.getWriter().print("session创建成功，session的id是："+sessionId);
        }else {
            response.getWriter().print("服务器已经存在该session了，session的id是："+sessionId);
        }
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }
}
```

	第一次访问时，服务器会创建一个新的sesion，并且把session的Id以cookie的形式发送给客户端浏览器，如下图所示：

　　点击刷新按钮，再次请求服务器，此时就可以看到浏览器再请求服务器时，会把存储到cookie中的session的Id一起传递到服务器端了，如下图所示：

　　我猜想request.getSession()方法内部新创建了Session之后一定是做了如下的处理

```text
//获取session的Id
String sessionId = session.getId();
//将session的Id存储到名字为JSESSIONID的cookie中
Cookie cookie = new Cookie("JSESSIONID", sessionId);
//设置cookie的有效路径
cookie.setPath(request.getContextPath());
response.addCookie(cookie);
```

## 四、浏览器禁用Cookie后的session处理

### 4.1、IE8禁用cookie

```text
　　工具->internet选项->隐私->设置->将滑轴拉到最顶上（阻止所有cookies）
```

### 4.2、解决方案：URL重写

**response.encodeRedirectURL(java.lang.String url)** 用于对sendRedirect方法后的url地址进行重写。
　　**response.encodeURL(java.lang.String url)**用于对表单action和超链接的url地址进行重写

### 4.3、范例：禁用Cookie后servlet共享Session中的数据

**IndexServlet**

```text
//首页：列出所有书
public class IndexServlet extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        //创建Session
        request.getSession();
        out.write("本网站有如下书：<br/>");
        Set<Map.Entry<String,Book>> set = DB.getAll().entrySet();
        for(Map.Entry<String,Book> me : set){
            Book book = me.getValue();
            String url =request.getContextPath()+ "/servlet/BuyServlet?id=" + book.getId();
            //response. encodeURL(java.lang.String url)用于对表单action和超链接的url地址进行重写
            url = response.encodeURL(url);//将超链接的url地址进行重写
            out.println(book.getName()  + "   <a href='"+url+"'>购买</a><br/>");
        }
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }
}


/**
 * @author gacl
 * 模拟数据库
 */
class DB{
    private static Map<String,Book> map = new LinkedHashMap<String,Book>();
    static{
        map.put("1", new Book("1","javaweb开发"));
        map.put("2", new Book("2","spring开发"));
        map.put("3", new Book("3","hibernate开发"));
        map.put("4", new Book("4","struts开发"));
        map.put("5", new Book("5","ajax开发"));
    }
    
    public static Map<String,Book> getAll(){
        return map;
    }
}

class Book{
    
    private String id;
    private String name;

    public Book() {
        super();
    }
    public Book(String id, String name) {
        super();
        this.id = id;
        this.name = name;
    }
    public String getId() {
        return id;
    }
    public void setId(String id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

**BuyServlet**

```text
public class BuyServlet extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String id = request.getParameter("id");
        Book book = DB.getAll().get(id);  //得到用户想买的书
        HttpSession session = request.getSession();
        List<Book> list = (List) session.getAttribute("list");  //得到用户用于保存所有书的容器
        if(list==null){
            list = new ArrayList<Book>();
            session.setAttribute("list", list);
        }
        list.add(book);
        //response. encodeRedirectURL(java.lang.String url)用于对sendRedirect方法后的url地址进行重写
        String url = response.encodeRedirectURL(request.getContextPath()+"/servlet/ListCartServlet");
        System.out.println(url);
        response.sendRedirect(url);
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }

}
```

**ListCartServlet**

```text
public class ListCartServlet extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        HttpSession session = request.getSession();
        List<Book> list = (List) session.getAttribute("list");
        if(list==null || list.size()==0){
            out.write("对不起，您还没有购买任何商品!!");
            return;
        }
        
        //显示用户买过的商品
        out.write("您买过如下商品:<br>");
        for(Book book : list){
            out.write(book.getName() + "<br/>");
        }
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }
}
```

在禁用了cookie的IE8下的运行效果如下：

通过查看IndexServlet生成的html代码可以看到，每一个超链接后面都带上了session的Id，如下所示

```text
1 本网站有如下书：<br/>javaweb开发   <a href='/JavaWeb_Session_Study_20140720/servlet/BuyServlet;jsessionid=96BDFB9D87A08D5AB1EAA2537CDE2DB2?id=1'>购买</a><br/>
2 spring开发   <a href='/JavaWeb_Session_Study_20140720/servlet/BuyServlet;jsessionid=96BDFB9D87A08D5AB1EAA2537CDE2DB2?id=2'>购买</a><br/>
3 hibernate开发   <a href='/JavaWeb_Session_Study_20140720/servlet/BuyServlet;jsessionid=96BDFB9D87A08D5AB1EAA2537CDE2DB2?id=3'>购买</a><br/>
4 struts开发   <a href='/JavaWeb_Session_Study_20140720/servlet/BuyServlet;jsessionid=96BDFB9D87A08D5AB1EAA2537CDE2DB2?id=4'>购买</a><br/>
5 ajax开发   <a href='/JavaWeb_Session_Study_20140720/servlet/BuyServlet;jsessionid=96BDFB9D87A08D5AB1EAA2537CDE2DB2?id=5'>购买</a><br/>
```

所以，当浏览器禁用了cookie后，就可以用URL重写这种解决方案解决Session数据共享问题。而且response. encodeRedirectURL(java.lang.String url) 和response. encodeURL(java.lang.String url)是两个非常智能的方法，当检测到浏览器没有禁用cookie时，那么就不进行URL重写了。

## 五、session对象的创建和销毁时机

### 5.1、session对象的创建时机

　　在程序中第一次调用request.getSession()方法时就会创建一个新的Session，可以用isNew()方法来判断Session是不是新创建的

范例：创建session

```text
//使用request对象的getSession()获取session，如果session不存在则创建一个
HttpSession session = request.getSession();
//获取session的Id
String sessionId = session.getId();
//判断session是不是新创建的
if (session.isNew()) {
    response.getWriter().print("session创建成功，session的id是："+sessionId);
}else {
    response.getWriter().print("服务器已经存在session，session的id是："+sessionId);
}
```

### 5.2、session对象的销毁时机

　　session对象默认30分钟没有使用，则服务器会自动销毁session，在web.xml文件中可以手工配置session的失效时间，例如：

```text
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" 
    xmlns="http://java.sun.com/xml/ns/javaee" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
    http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
  <display-name></display-name>
  
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>

  <!-- 设置Session的有效时间:以分钟为单位-->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>

</web-app>
```

当需要在程序中手动设置Session失效时，可以手工调用**session.invalidate**方法，摧毁session。

```text
1 HttpSession session = request.getSession();
2 //手工调用session.invalidate方法，摧毁session
3 session.invalidate();
```