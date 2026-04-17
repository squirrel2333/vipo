# 什么是EL

Expression Language 表达语言

EL表达式主要是代替jsp页面中的表达式脚本在jsp页面中进行数据的舒服。因为EL表达式更加简洁

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        request.setAttribute("key", "值");
    %>
    表达式脚本输出key的值：<%=request.getAttribute("key")%> <br/>
    表达式脚本输出key的值：<%=request.getAttribute("key1")%> <br/> 输出null
    EL表达：${key}
    EL表达：${key1}  输出空串
</body>
</html>
```
# EL表达式搜索域数据的顺序

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        pageContext.setAttribute("key", "pageContext");
        request.setAttribute("key", "request");
        session.setAttribute("key", "session");
        application.setAttribute("key", "application");
    %>
    ${key}
</body>
</html>
```
从pageContext ---->application

