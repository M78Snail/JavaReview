## 上传图片:

### 1\)在tomcat中配置虚拟图片服务器

* 在tomcat上配置图片虚拟目录，在tomcat下conf/server.xml中添加：

```java
<Context docBase="ssmDemo" path="/ssmDemo" reloadable="true"
    source="org.eclipse.jst.jee.server:ssmDemo" />
<Context docBase="E:\image" path="/pic" reloadable="true" />
```

访问[http://localhost:8080/pic即可访问E:\image下的图片。](http://localhost:8080/pic即可访问E:\image下的图片。)

* 通过eclipse配置

![](/assets/importsmvc8.1.png)

### 2\)导入fileupload的jar包

CommonsMultipartResolver解析器依赖commons-fileupload和commons-io，加入如下jar包：

![](/assets/importsmvc8.2.png)

### 3\)在springMvc.xml中配置上传组件

```java
<bean id="multipartResolver"
    class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- 设置上传文件的最大尺寸为5MB -->
    <property name="maxUploadSize">
        <value>5242880</value>
    </property>
</bean>
```

### 4\)在页面上编写上传域,更改form标签的类型

```java
<form id="itemForm"
    action="${pageContext.request.contextPath }/editItemSubmit.action"
    method="post" enctype="multipart/form-data">
</form>
```

```java
<tr>
    <td>商品图片</td>
    <td><c:if test="${item.pic !=null}">
            <img src="/pic/${item.pic}" width=100 height=100 />
            <br />
        </c:if> <input type="file" name="pictureFile" /></td>
</tr>
```

### 5\)在controller方法中可以使用MultiPartFile接口接收上传的图片

```java
@RequestMapping("/editItemSubmit")
public String editItemSubmit(Items items, MultipartFile pictureFile) throws Exception {
	// 原文件名称
	String pictureFileName = pictureFile.getOriginalFilename();
	// 新文件名称
	String newFileName = UUID.randomUUID().toString() + pictureFileName.substring(pictureFileName.lastIndexOf("."));
	// 上传图片
	File uploadPic = new File("E:\\images" + newFileName);

	if (!uploadPic.exists()) {
		uploadPic.mkdirs();
	}
	// 向磁盘写文件
	pictureFile.transferTo(uploadPic);

	// 将图片名称写入数据库
	items.setPic(newFileName);
	itemsService.updateItems(items);

	return "redirect:itemEdit/" + items.getId();
}
```



