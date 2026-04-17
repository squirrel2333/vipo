
### web开发

按照web资源分类：

* 静态web
+ html，css
+ 提供给所有人看的数据不会变化
+ 只在浏览器编译与执行
+ 静态文件被索要时，Http服务器直接通过输出流将静态文件中内容以二进制传给浏览器
* 动态web
+ 几乎所有网站，会发生动态变化
+ Servlet/JSP，ASP，PHP（.class)
+ 只在服务器端编译执行
+ 动态文件被索要时，Http服务器需要创建当前的class文件的实例对象调用对应的方法处理用户请求

Java中，web资源开发统称JavaWeb

### Web应用程序

可供浏览器访问的程序

* a.html.....web资源被外界访问
* 任何资源和页面都存在具体的web程序
* URL
* 这个同意的web资源被放在一个文件夹下，web应用程序--->Tomcat
* 一个web由多部分组成（静态web，动态web）
+ html,css,js
+ jsp,servlet
+ java程序
+ jar包
+ 配置文件
* web应用若想外部访问，需要服务器统一管理

### 静态web

![](Web基本概念_001.png)

* 缺点：web页面无法动态更新
+ 用户看到的一样
- 轮播图、点击特效：伪动态
- JavaScript
- VBScript
+ 无法和数据库交互（数据无法持久化）

### 动态web

![](Web基本概念_002.png)

缺点：

* web资源错误，需要停机维护（重新编写后台程序）

优点

* web页面动态更新
* 可以与数据库交互

### 常用的web服务器

* Tomcat
* Jboss
* GlassFish
* Resin
* WebLogic
