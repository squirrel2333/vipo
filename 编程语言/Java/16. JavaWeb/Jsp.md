jsp(java server pages) java服务器页面。主要作用是代替Servlet程序回传html页面数据。因为Servlet回传html是一件很繁琐的事情，开发和维护成本极高（例如要在doGet方法中不断的写writer.write(" <body>/r/n")才能将html数据回传). 比较老式被淘汰了

# 如何创建JSP

1. 在webapp下面创建jsp文件
2. jsp和html一样都是存放在web目录下，访问也和html页面一样 http://ip:port/工程路径/b.jsp
3. jsp页面本质是一个servlet程序。当我们第一次访问jsp页面，tomcat服务器会将jsp文件翻译为java文件，并且进行编译成为class字节码程序。jsp源码是HttJspBase继承HttpServlet

# Jsp语法

## jsp头部的page指令

<%@ **page**contentType="text/html;charset=UTF-8" language="java" %>

Jsp的page指令可以修改jsp页面中的一些重要属性

1. language属性： 暂时只支持Java
2. contentType属性：jsp返回类型是什么，也是源码中response.setContextType设置的类型
3. pageEncoding: gbk utf-8.... jsp页面本身的字符集
4. import: 与java一样，导包
5. autoFlush: "true" / "false" 设置out输出流缓冲区满了是否自动刷新
6. buffer: 缓冲区大小
7. errorPage：运行时出错，自动跳转到页面
8. isErrorPage： 设置当前jsp页面是否是错误信息页面，默认是false，如果是true可以获取异常信息
9. session：设置当前jsp页面是否会创建session对象，默认true
10. extends：jsp翻译出来的java类默认继承谁

```java
<%@ page import="java.util.Map" %>
<%@ page contentType="text/html;charset=UTF-8" language="java"
         errorPage="/error500.jsp" %> <!-- 映射到工程路径/ -->
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        int i = 10 / 0;
    %>
</body>
</html>
```
## jsp中的脚本

### 声明脚本

1. 声明类属性
2. 声明static静态代码
3. 声明类方法
4. 声明内部类

```java
<body>
    <%!
        private Integer id;
        private String name;
        private static Map<String, Object> map;
    %>
    <%!
        static {
            map = new HashMap<>();
            map.put("key", "value");
        }
    %>
    <%!
        public int hi() {
            return 12;
        }
    %>
    <%!
        public static class A {
            private Integer id;
            private String abc;
        }
    %>
</body>
```
### 表达式脚本

输出jsp到页面数据

1. 输出整形
2. 输出浮点型
3. 输出字符串
4. 输出对象

```java
<%=12 %> <br>
<%=12.12 %> <br>
<%="字符串"%> <br>
<%=map%>
<%=request.getParameter("username")%>
```
1. 所有表达式都被翻译到\_jspService()方法中
2. 表达式脚本都被翻译到out.print()输出页面上
3. 由于表达式脚本都在方法中，所以方法中的对象可以直接调用
4. 表达式脚本中的表达式不要以分号结束

### 代码脚本

```java
<%
  java 语句
%>
```
1. 代码脚本翻译之后都在\_jspService方法中
2. 代码脚本由于翻译到方法中，\_jspService 中的现有对象都可以直接使用
3. 还可以由多个代码脚本块组合成完整的java语句
4. 代码脚本可以和表达式脚本一起结合使用，一起输出

## Jsp中的三种注释

1. html注释

`<!-- -->` 被翻译到java源码中，\_jspService方法中，以out.writer输出到页面

2. java注释

`//`

`/\* \*/`

java注释会被翻译到源码中，当做注释

3. jsp注释

`<%-- --%>`

# jsp九个内置对象

* request 请求对象
* response 响应对象
* pageContext jsp上下文
* session 会话对象
* application ServletContext对象
* config ServletConfig对象
* out jsp输出流对象
* page 指向当前jsp的对象
* exception 异常对象

## jsp四大域对象

域对象是可以像Map一样存取数据的对象。四个域对象功能都一样，不同的是数据存储范围

* pageContext: PageContextImpl 当前jsp页面范围有效
* request: HttpServletRequest 一次请求有效
* session: HttpSession 一次会话有效（打开浏览器，直到关闭浏览器）
* application: ServletContext 整个web工程内都有效（只要项目不停止）

```java
<%
  pageContext.setAttribute("key", "pageContext");
  request.setAttribute("key", "request");
  session.setAttribute("key", "session");
  application.setAttribute("key", "application");
%>
```
使用的优先顺序 pageContext > request > sessioin > application 减轻内存压力

# jsp的out输出和response.getWrtier区别

```java
<%
  out.write("out输出1 <br/>");
  out.write("out输出2 <br/>");
  response.getWriter().write("response输出1 <br/>");
  response.getWriter().write("response输出2 <br/>");
%>
```
当jsp页面中代码执行完

1. 执行out.flush()操作 会把out缓冲区的数据追加写入到response缓冲区末尾
2. 会执行response的刷新操作，把数据写给客户端

由于底层源码，都是使用out来输出，所以一般，jsp统一使用out输出，避免打乱顺序

`out.write(12)` 输出：♀

`out.print(12)` 输出：12

print是将数据转换为字符串调用write方法。因为write会转换为字符asci码

# jsp常用标签

## 静态包含

```java
<head>
  <title>Title</title>
</head>
<body>
  头部信息<br>
  主体内容<br>
  <%@ include file="/include/footer.jsp"%>  <%-- 第一个斜杠表示http:ip:port/工程/ 映射到web目录 --%>
</body>
</html>
```
1. 静态包含不会翻译被包含的jsp页面
2. 静态包含其实是把被包含的jsp页面的代码拷贝到包含的位置执行输出

## 动态包含

```java
<jsp:include page="/include/footer.jsp"></jsp:include>
```
动态包含的特点：

1. 动态包含会把被包含的jsp页面翻译为java代码
2. 动态包含底层调用被包含的jsp页面执行输出

## 转发

```java
<jsp:forward page"=/scope.jsp"></jsp.forward>
```
