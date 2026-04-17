
1. Filter过滤器是JavaWeb三大组件之一。三大组件分别是Servlet、Listener监听器、Filter过滤器
2. 是JavaEE规范，也就是接口
3. 作用是拦截请求
4. 应用场景
1. 权限检查
2. 日记操作
3. 事务管理
4. ...

# Filter初步

要求：在web工程下，有一个admin目录，这个admin目录下的所有资源都必须在用户登录后允许访问

思路：只要把登录信息保存到Session中。检查用户是否登录，判断Session是否包含用户信息

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

    <%
        Object user = session.getAttribute("user");
        if (null == user) {
            request.getRequestDispatcher("/login.jsp").forward(request, response);
            return;
        }

    %>
    我是a.jsp
</body>
</html>
```
如果没有登录信息，就跳转到登录页面

这样做有局限性，只能在jsp文件中

# Filter完整用户登录检查

![](Filter过滤器_001.png)

![](Filter过滤器_002.png)

**Filter过滤器的使用步骤**

1. 编写一个类去实现Filter接口
2. 实现过滤的方法doFilter
3. 到web.xml中去配置Filter的拦截路径

```java
package com.example.filter;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import java.io.IOException;

public class AdminFilter implements Filter {

  /**
   * 用于拦截请求
   *
   * @param servletRequest
   * @param servletResponse
   * @param filterChain
   * @throws IOException
   * @throws ServletException
   */
  @Override
  public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
      HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
      HttpSession session = httpServletRequest.getSession();
      Object user = session.getAttribute("user");
      if (null == user) {
          servletRequest.getRequestDispatcher("/login.jsp").forward(servletRequest, servletResponse);
          return;
      } else {
          // 让程序继续向下访问.必须要有这步。否则请求到此为止
          filterChain.doFilter(servletRequest, servletResponse);
      }
  }
}
```

```java
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html; charset=UTF-8");
        String username = request.getParameter("username");
        String password = request.getParameter("password");

        if ("hqw".equals(username) && "123456".equals(password)) {
            request.getSession().setAttribute("user", username);
            response.getWriter().write("登录成功");
        } else {
            request.getRequestDispatcher("/login.jsp").forward(request, response);
        }

    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }
}
```

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    登录页面
    <form action="http://localhost:8080/filter/login" method="get">
        用户名：<input type="text" name="username"/> <br>
        密 码：<input type="password" name="password"/> <br>
        <input type="submit"/>
    </form>
</body>
</html>
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <filter>
        <filter-name>AdminFilter</filter-name>
        <filter-class>com.example.filter.AdminFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>AdminFilter</filter-name>
        <!-- 拦截路径 /代表http://ip:port/工程路径/ 映射到IDEA的web目录 -->
        <url-pattern>/admin/*</url-pattern>
    </filter-mapping>
    <servlet>
        <servlet-name>LoginServlet</servlet-name>
        <servlet-class>com.example.servlet.LoginServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>LoginServlet</servlet-name>
        <url-pattern>/login</url-pattern>
    </servlet-mapping>
</web-app>
```
![](Filter过滤器_003.png)

**Debug步骤：**

1. 浏览器访问filter/admin/a.html
2. 由filter执行AdminFilter.class
3. 此时session中没有user信息，重定位到login.jsp
4. 在jsp页面中输入表单，提交到/login 即 LoginServlet.class
1. LoginServlet中判断username password正确性，不正确跳转回login.jsp
2. 如果输入正确往session中写入user信息
5. 再次尝试登录a.html，此时已经有session信息，doFilter中判断，让程序继续向下访问

# Filter的生命周期

Filter生命周期包含几个方法

1. 构造器方法
2. init初始化
3. doFilter过滤方法
4. destroy销毁方法，工程停止时就会执行销毁方法

# FilterConfig类

Filter过滤器的配置文件

Tomcat每次创建Filter的时候，也会创建一个FilterConfig类，这里包含了Filter配置文件的想信息

作用：

1. 获取Filter名称filter-name的内容

`filterConfig.getFilterName()`

2. 获取Filter配置的init-param初始化参数

```java
    <filter>
        <filter-name>AdminFilter</filter-name>
        <filter-class>com.example.filter.AdminFilter</filter-class>

        <init-param>
            <param-name>username</param-name>
            <param-value>root</param-value>
        </init-param>
        <init-param>
            <param-name>url</param-name>
            <param-value>jdbc:mysql://localhost:3306/test</param-value>
        </init-param>
    </filter>
```
`filterConfig.getInitParameter("username")`

3. 获取ServletContext对象

`filterConfig.getServletContext()`

# FilterChain过滤器链

![](Filter过滤器_004.png)

1. 在多个过滤器执行的时候，顺序取决于web.xml中配置的顺序
2. 所有filter步骤和目标资源都默认在同一个线程中
3. 多个Filter共同执行时，使用的是同一个request对象

# 注解方式配置过滤器

![](Filter过滤器_005.png)

![](Filter过滤器_006.png)

