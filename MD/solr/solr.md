```java
cp /usr/local/solr/solr-4.10.3/dist/solr-4.10.3.war /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr.war
```

复制jar包

```java
cp /usr/local/solr/solr-4.10.3/example/lib/ext/* /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/WEB-INF/lib/
```

配置solrhome

```java
cp -r /usr/local/solr/solr-4.10.3/example/solr /usr/local/solr/solrhome
```

编辑web配置文件

```java
vim /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/WEB-INF/web.xml
```

打开JNDI，修改solrhome位置

```java
 <env-entry>
       <env-entry-name>solr/home</env-entry-name>
       <env-entry-value>/usr/local/solr/solrhome</env-entry-value>
       <env-entry-type>java.lang.String</env-entry-type>
 </env-entry>
```

---

## IK

复制jar包

```java
cp /root/IK\ Analyzer\ 2012FF_hf1/IKAnalyzer2012FF_u1.jar /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/WEB-INF/lib/
```

创建classes文件夹

```java
mkdir /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/WEB-INF/classes
```

复制

```java
cp IKAnalyzer.cfg.xml ext_stopword.dic mydict.dic /usr/local/tomcat/apache-tomcat-7.0.47/webapps/solr/WEB-INF/classes/
```

定义业务域

```java
vim /usr/local/solr/solrhome/collection1/conf/schema.xml
```

添加

```java
<!-- IKAnalyzer-->
<fieldType name="text_ik" class="solr.TextField">
  <analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>
</fieldType>

<field name="item_title" type="text_ik" indexed="true" stored="true"/>
<field name="item_sell_point" type="text_ik" indexed="true" stored="true"/>
<field name="item_price"  type="long" indexed="true" stored="true"/>
<field name="item_image" type="string" indexed="false" stored="true" />
<field name="item_category_name" type="string" indexed="true" stored="true" />

<field name="item_keywords" type="text_ik" indexed="true" stored="false" multiValued="true"/>
<copyField source="item_title" dest="item_keywords"/>
<copyField source="item_sell_point" dest="item_keywords"/>
<copyField source="item_category_name" dest="item_keywords"/>
```

---

## 使用

### solr配置文件

```
<!-- 单机版solr的连接 -->
<!-- <bean id="httpSolrServer" class="org.apache.solr.client.solrj.impl.HttpSolrServer">
    <constructor-arg name="baseURL" value="http://192.168.25.130:8080/solr/collection1"/>
</bean> -->

<!-- 集群版solr的连接 -->
<bean id="cloudSolrServer" class="org.apache.solr.client.solrj.impl.CloudSolrServer">
    <constructor-arg name="zkHost" value="192.168.25.130:2181,192.168.25.130:2182,192.168.25.130:2183"/>
</bean>
```

### 导入

```java
// 1、先查询所有商品数据
List<SearchItem> itemList = searchItemMapper.getItemList();
// 2、遍历商品数据添加到索引库
for (SearchItem searchItem : itemList) {
        // 创建文档对象
        SolrInputDocument document = new SolrInputDocument();
    // 向文档中添加域
    document.addField("id", searchItem.getId());
    document.addField("item_title", searchItem.getTitle());
    document.addField("item_sell_point", searchItem.getSell_point());
    document.addField("item_price", searchItem.getPrice());
    document.addField("item_image", searchItem.getImage());
    document.addField("item_category_name", searchItem.getCategory_name());
    document.addField("item_desc", searchItem.getItem_desc());
    // 把文档写入索引库
    solrServer.add(document);
}
// 3、提交
solrServer.commit();
```

### 查询

```java
//根据查询条件拼装查询对象
//创建一个SolrQuery对象
SolrQuery query = new SolrQuery();
//设置查询条件
query.setQuery(queryString);
//设置分页条件
if (page < 1) page =1;
query.setStart((page - 1) * rows);
if (rows < 1) rows = 10;
query.setRows(rows);
//设置默认搜索域
query.set("df", "item_title");
//设置高亮显示
query.setHighlight(true);
query.addHighlightField("item_title");
query.setHighlightSimplePre("<font color='red'>");
query.setHighlightSimplePost("</font>");
```

```java
//根据query对象进行查询
QueryResponse response = solrServer.query(query);
//取查询结果
SolrDocumentList solrDocumentList = response.getResults();
//取查询结果总记录数
long numFound = solrDocumentList.getNumFound();
SearchResult result = new SearchResult();
result.setRecordCount(numFound);
List<SearchItem> itemList = new ArrayList<>();
//把查询结果封装到SearchItem对象中
for (SolrDocument solrDocument : solrDocumentList) {
    SearchItem item = new SearchItem();
    item.setCategory_name((String) solrDocument.get("item_category_name"));
    item.setId((String) solrDocument.get("id"));
    //取一张图片
    String image = (String) solrDocument.get("item_image");
    if (StringUtils.isNotBlank(image)) {
        image = image.split(",")[0];
    }
    item.setImage(image);
    item.setPrice((long) solrDocument.get("item_price"));
    item.setSell_point((String) solrDocument.get("item_sell_point"));
    //取高亮显示
    Map<String, Map<String, List<String>>> highlighting = response.getHighlighting();
    List<String> list = highlighting.get(solrDocument.get("id")).get("item_title");
    String title = "";
    if (list != null && list.size() > 0) {
        title = list.get(0);
    } else {
        title = (String) solrDocument.get("item_title");
    }
    item.setTitle(title);
    //添加到商品列表
    itemList.add(item);
}
```



