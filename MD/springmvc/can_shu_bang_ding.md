### 默认类型

springMvc中默认支持的参数类型:也就是说在controller方法中可以加入这些也可以不加, 加不加看自己需不需要,都行.

> * HttpServletRequest
>
> * HttpServletResponse
>
> * HttpSession
>
> * Model

```java
@RequestMapping("/itemEdit")
public String itemEdit(HttpServletRequest request, Model model) throws Exception {

       String idStr = request.getParameter("id");

       Items items = itemsService.findItemsById(Integer.parseInt(idStr));

       model.addAttribute("item", items);

       return "editItem";
}
```

---

### 基本类型

> * springMvc可以直接接收基本数据类型,包括string.spirngMvc可以帮你自动进行类型转换.
>
> * controller方法接收的参数的变量名称必须要等于页面上input框的name属性值
>
> * spirngMvc可以直接接收pojo类型:要求页面上input框的name属性名称必须等于pojo的属性名称

```java
@RequestMapping("/updateitem")
public String updateitem(Integer id, String name, Float price, String detail) throws Exception {
       Items items = new Items();
       items.setId(id);
       items.setName(name);
       items.setPrice(price);
       items.setDetail(detail);
       items.setCreatetime(new Date());
       itemsService.updateItems(items);
       return "success";
}
```

---

### pojo类型

```java
@RequestMapping("/updateitem")
public String update(Items items) throws Exception{
       items.setCreatetime(new Date());
       itmesService.updateItems(items);
       return "success";
}
```

---

### Vo类型（多属性查询）

如果Controller中接收的是Vo,那么页面上input框的name属性值要等于vo的属性.属性.属性

```java
public class QueryVo {
    //商品对象
    private Items items;
    //订单对象...
    //用户对象....

    public Items getItems() {
        return items;
    }

    public void setItems(Items items) {
        this.items = items;
    }

}
```

```java
<table width="100%" border=1>
        <tr>
        <!-- 如果Controller中接收的是Vo,那么页面上input框的name属性值要等于vo的属性.属性.属性..... -->
            <td>商品名称:<input type="text" name="items.name" /></td>
            <td>商品价格:<input type="text" name="items.price" /></td>
            <td><input type="submit" value="查询" /></td>
        </tr>
</table>
```

```java
@RequestMapping("/search")
public String search(QueryVo vo) throws Exception{
       System.out.println(vo);
       return "";
}
```

---

### 自定义转换器

```java
public class CustomGlobalStrToDateConverter implements Converter<String, Date> {

    @Override
    public Date convert(String source) {
        try {
            Date date = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss").parse(source);
            return date;
        } catch (ParseException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return null;
    }

}
```

```java
<mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>

<!-- 配置自定义转换器 注意: 一定要将自定义的转换器配置到注解驱动上 -->
<bean id="conversionService"
    class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
    <property name="converters">
        <set>
            <!-- 指定自定义转换器的全路径名称 -->
            <bean class="cn.itheima.controller.converter.CustomGlobalStrToDateConverter" />
        </set>
    </property>
</bean>
```



