## controller方法返回值\(指定返回到哪个页面, 指定返回到页面的数据\)

### 1\)ModelAndView

* modelAndView.addObject\("itemList", list\); 指定返回页面的数据

* modelAndView.setViewName\("itemList"\);      指定返回的页面

### 2\)String\(推荐使用\)

* 返回普通字符串,就是页面去掉扩展名的名称, 返回给页面数据通过Model来完成

* 返回的字符串以forward:开头为请求转发

* 返回的字符串以redirect:开头为重定向

### 3\)返回void\(使用它破坏了springMvc的结构,所以不建议使用\)

* 可以使用request.setAttribut 来给页面返回数据

* 可以使用request.getRquestDispatcher\(\).forward\(\)来指定返回的页面

* 如果controller返回值为void则不走springMvc的组件,所以要写页面的完整路径名称

---

## 转发

> **请求转发:**浏览器中url不发生改变,request域中的数据可以带到转发后的方法中

```java
return "redirect:itemEdit/"+items.getId();
```

> **重定向:**浏览器中url发生改变,request域中的数据不可以带到重定向后的方法中

```
return "forward:itemEdit/"+items.getId();
```

由于forward中request不能传递值，显然model传数据更好。

---

### 路径

> * 相对路径:相对于当前目录,也就是在当前类的目录下,这时候可以使用相对路径跳转
>
> * 绝对路径:从项目名后开始.

**在springMvc中不管是forward还是redirect后面凡是以/开头的为绝对路径,不以/开头的为相对路径**

* 后面forward:itemEdit.action表示相对路径,相对路径就是相对于当前目录,当前为类上面指定的items目录.在当前目录下可以使用相对路径随意跳转到某个方法中

* 后面forward:/itemEdit.action路径中以斜杠开头的为绝对路径,绝对路径从项目名后面开始算



