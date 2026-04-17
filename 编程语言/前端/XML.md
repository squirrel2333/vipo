### 特点：

1. XML必须以根目录标签开始，其它标签作为根目录标签的直接子标签或者间接子标签出现
2. HTML标签对大小写忽略不计，但是XML必须区分大小写
3. HTML中属性通过""包含，也可以不用，XML中必须包含在''或""中

### XML读取方式

1. SAX：一次将若干满足条件的标签加载到内存
* 优点：节省内存
* 缺点：大量标签信息时，运行效率较低

2. DOM：一次将xml文档所有内容加载
* 缺点：浪费内存
* 优点：读取大量标签信息，运行较快

一般用DOM

### 约束文档

1. 作用：
1. 设置XML文档声明（标签类型名）
2. 设置标签中出现的属性名
3. 设置标签之间父子关系
2. 分类：
1. DTD约束文档：简单约束文档
2. SCHEMA约束文档：高级约束文档

### 实体引用

避免解析器识别错

|  |  |  |
| --- | --- | --- |
| &lt; | < | 小于 |
| &gt; | > | 大于 |
| &amp; | & | 和号 |
| &apos; | ' | 单引号 |
| &quot; | " | 引号 |

### 命名空间

在 XML 中，元素名称是由开发者定义的，当两个不同的文档使用相同的元素名时，就会发生命名冲突。

#### 使用前缀避免冲突

此文档带有某个表格中的信息：

```
<h:table>
   <h:tr>
   <h:td>Apples</h:td>
   <h:td>Bananas</h:td>
   </h:tr>
</h:table>
```
此 XML 文档携带着有关一件家具的信息：

```
<f:table>
   <f:name>African Coffee Table</f:name>
   <f:width>80</f:width>
   <f:length>120</f:length>
</f:table>
```
现在，命名冲突不存在了，这是由于两个文档都使用了不同的名称来命名它们的 <table> 元素 (<h:table> 和 <f:table>)。

通过使用前缀，我们创建了两种不同类型的 <table> 元素。

#### 使用命名空间（Namespaces）

这个 XML 文档携带着某个表格中的信息：

```
<table xmlns="http://www.w3.org/TR/html4/">
   <h:tr>
   <h:td>Apples</h:td>
   <h:td>Bananas</h:td>
   </h:tr>
</h>
```
此 XML 文档携带着有关一件家具的信息：

```
<table xmlns="http://www.w3school.com.cn/furniture">
   <f:name>African Coffee Table</f:name>
   <f:width>80</f:width>
   <f:length>120</f:length>
</f>
```
与仅仅使用前缀不同，我们为 <table> 标签添加了一个 xmlns 属性，这样就为前缀赋予了一个与某个命名空间相关联的限定名称。

**XML 命名空间属性被放置于元素的开始标签之中，并使用以下的语法：**

```
xmlns:namespace-prefix="namespaceURI"
```
当命名空间被定义在元素的开始标签中时，所有带有相同前缀的子元素都会与同一个命名空间相关联。

### DTD文档

```
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE web-app SYSTEM "web-app.dtd">

<!ELEMENT web-app (servlet*, servlet-mapping*, welcome-file-list?) >
<!ELEMENT 标签类型名 (父标签内的子标签名?)> : 声明可以在xml文档中出现标签类型名,
?表示可以出现在标签内部也可以不出现，如果出现只能有一次；
+子标签必须出现在父标签内部，且可以出现多次；
#PCDATA 当前标签没有子标签
*表示可以出现在标签内部也可以不出现，且可以出现多次；
没有修饰表示表示必须出现在内部，且只能出现一次
(子标签|子标签2)表示必须有一个存在，但不能同时出现

<!ATTLIST web-app version CDATA #IMPLIED>
<!ATTLIST 标签类型名 属性名>: 声明当前标签内的属性名
```
### SCHEMA

```
<?xml version="1.0"?>

<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
<! --显示 schema 中用到的元素和数据类型来自命名空间 "http://www.w3.org/2001/XMLSchema"。同时它还规定了来自命名空间的元素和数据类型应该使用前缀 xs：-->
targetNamespace="http://www.w3school.com.cn"
<! --显示被此 schema 定义的元素 (note, to, from, heading, body) 来自命名空间： "http://www.w3school.com.cn"。-->
xmlns="http://www.w3school.com.cn"
<! --指出默认的命名空间是 "http://www.w3school.com.cn"。-->
elementFormDefault="qualified">
<!-- 指出任何 XML 实例文档所使用的且在此 schema 中声明过的元素必须被命名空间限定。-->

<xs:element name="web-app">
    <xs:complexType>
      <xs:sequence>
	<xs:element name="to" type="xs:string"/>
	<xs:element name="from" type="xs:string"/>
	<xs:element name="heading" type="xs:string"/>
	<xs:element name="body" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
</xs:element>

</xs:schema>
```
