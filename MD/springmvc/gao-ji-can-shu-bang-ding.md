### 数组

* 如果批量删除,一堆input复选框,那么可以提交数组.\(只有input复选框被选中的时候才能提交\)
* checkBox的name属性名称要等于Vo中接收的属性名

### List

* 可以用List&lt;Pojo&gt;来接收，页面上input框的name属性值=Vo中接收的属性名称+\[list的下标\]+list泛型属性的名称

### @RequestParam

```java
@RequestMapping("/updateitem")
public@RequestParam(value = "id", defaultValue = "") Integer id, String updateitem(Integer id, String name, Float price, String detail) throws Exception {

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



