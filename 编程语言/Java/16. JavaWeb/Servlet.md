
# Servlet 简介

* sun公司开发的web技术，是JavaEE规范之一，规范就是接口
* Servlet是JavaWeb三大组件之一：Servlet程序、Filter过滤器、Listener监听器
* 是运行在Java上的程序。他可以接收客户端请求，响应给客户端
* Sun在这些API中提供一个接口叫做Servlet，如果想开发
+ 编写一个类实现Servlet接口
+ 把开发好的Java类部署到web服务器中
+ 把实现了Servlet接口的Java程序叫做Servlet

# HelloServlet

## 手动实现Servlet

1. 新建Java类

```java
package com.hqw.test1;

import javax.servlet.*;
import java.io.IOException;

public class HelloServlet2 implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("servlet被访问了");
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        String method = httpServletRequest.getMethod();
        if ("GET".equals(method.toUpperCase())) {
            doGet();
        } else if (method.toLowerCase().equals("post")) {
            doPost();
        } else {
            System.out.printf("ERROR");
        }
    }

    public void doGet() {
    	System.out.printf("GET 请求");
    }

    public void doPost() {
    	System.out.printf("POST 请求");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}

```
* 生命周期
+ 执行Servlet构造器方法
+ 执行init方法
+ 执行service方法
+ 执行destroy方法

一二步在第一次访问时调用，第三部每次都会执行，第四部在web工程停止时调用

总结：

1. Servlet对象在Tomcat容器中是单例的
2. 容器在处理并发请求时，会在容器中开启一个线程。
3. 多线程可能会使用同一个Servlet对象，不要定义成员变量

webapp/WEB-INF/web.xml中

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- servlet标签给Tomcat配置servlet程序 -->
    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>com.hqw.test1.HelloServlet2</servlet-class>
    </servlet>

    <!--给servlet标签配置访问地址-->
    <servlet-mapping>
        <!--告诉服务器，当前配置的地址给哪个Servlet程序使用-->
        <servlet-name>HelloServlet</servlet-name>
        <!--配置访问地址  /斜杆表示http://ip:port/工程路径-->
        <!--http://ip:port/工程路径/hello-->
        <!--
            / 表示通配所有资源,不包括jsp文件
       			/* 表示通配所有资源,包括􏴠sp文件
      			/a/* 匹配所有以a前缀的映射路径
      			*.action 匹配所有以action为后缀的映射路径
        -->
        <url-pattern>/hello2</url-pattern>
    </servlet-mapping>
</web-app>
```
3. 编译运行 访问浏览器 url加上/hello2

### 请求分发

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action=hello2" method="get">
        <input type="submit">
    </form>
</body>
</html>
```
如果访问到工程目录/a.html -> 点击提交 -> 访问HelloServlet类 -> 解析出请求类型 -> 返回 “GET 请求”

## 通过继承HttpServlet实现Servlet程序

Servlet在Sun公司有两个默认的实现类**HttpServlet，GenericServlet**

GenericServlet实现Servlet，做了许多空的实现，并持有一个ServletConfig类的引用，并对ServletConfig的使用做了一些方法。

HttpServlet继承GenericServlet，并实现了请求的分发处理

**我们继承的类应该重写doGet(), doPost... 因为在HttpServlet中的service方法中, 将不同类型的请求调用了对应的处理方法doGet(),doPost()... 也可以重写service方法**

![](Servlet_001.svg)1. 构建一个普通的Maven项目，建立一个主工程
2. 关于父子工程的理解：
1. 父项目中会有

```java
  <modules>
    <module>servlet</module>
  </modules>
```
2. 子项目会有

```java
<parent>
</parent>
```
父项目中的java子项目可以直接使用

3. Maven环境优化
1. 修改web.xml为最新
2. 将maven结构搭建完整
4. 编写一个Servlet程序
1. 编写一个普通类
2. 实现Servlet接口

```java
public class HelloServlet extends HttpServlet {
    // 由于get或者post只是请求实现的不同方式，可以相互调用，业务逻辑都一样

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //ServletOutputStream outputStream = resp.getOutputStream();
        PrintWriter writer = resp.getWriter();
        writer.println("Hello, Servlet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```
HttpServlet类已经帮我们分发好请求了，调用get请求时就会进入doGet

5. 编写Servlet的映射

我们写到是Java程序，但是要通过浏览器访问，而浏览器需要连接web服务器，所以我们要在web服务中注册我们写的Servlet，还需要给他一个访问路径(web.xml中)

```java
  <!-- 注册Servlet -->
  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.huang.servlet.HelloServlet</servlet-class>
  </servlet>
  <!--Servlet请求路径-->
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
```
### 注解方式

```java
@WebServlet(
          name = "userServlet",
    	  //value urlPatterns两个属性作用相同，指定Servlet处理的url
          //value = "/user",
          urlPatterns = {"/userServlet1","/userServlet2","/userServlet"},
          initParams = {@WebInitParam(name = "encoding",value = "UTF-8")},
          loadOnStartup = 6
  )
```
1. 第一次被访问时，创建<load-on-startup>值为负数 或者 不配置
2. 在服务器启动时，创建<load-on-startup>值为0或正整数(表示实例化的顺序，如果重复，容器自行解决顺序，应该避免重复。系统已经启动实例化的servlet，自定义的尽量不要占用1-5）

```java
@WebServlet(name = "addServlet", value = "/addinfo")
public class AddServlet extends HttpServlet {
```
6. 配置Tomcat

注意：配置项目发布的路径

## 通过IDEA创建

1. 直接new servlet
2. web.xml 已经添加 servlet标签，需要加上servlet-mapping标签

# ServletConfig类

Servlet和ServletConfig对象都是由Tomcat负责创建，我们使用

Servlet程序默认是第一次访问时创建，ServletConfig也会在Servlet创建时创建

ServletConfig作用：

1. 获取Servlet程序的别名 servlet-name 的值
2. 获取初始化参数 init-param
3. 获取ServletContext对象

```java
public class HelloServlet implements Servlet {
    public HelloServlet(){}
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        super.init(servletConfig);
        // 获取程序别名
        System.out.println("程序别名是：" + servletConfig.getServletName()); //hello
        //获取初始化参数
        System.out.println("初始化参数username值：" + servletConfig.getInitParameter("username")); // root
        //获取ServletContext对象
        System.out.println(servletConfig.getServletContext());
    }
    ...
}

```

```java
<servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.huang.servlet.HelloServlet</servlet-class>
  	<init-param>
      <!-- 参数名 -->
      <param-name>username</param-name>
      <!-- 参数 -->
      <param-value>root</param-value>
    </init-param>
  </servlet>
  <!--Servlet请求路径-->
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
```
注意点：在重写init方法时，一定要调用super.init(servletConfig)。如果不调用，那原本GenericServlet中config的初始化就丢失了。那在其他位置直接调用getServletName()等操作时，就会空指针异常。

# ServletContext类

## 什么是ServletContext

1. ServletContext是一个接口，它表示Servlet的上下文
2. 一个web工程，**只有一个ServletContext对象实例**
3. ServletContext对象是一个域对象（可以像Map一样存取数据的对象）

 存数据 取数据 删除数据

Map put() get() remove()

域对象 setAttribute() getAttribute() removeAttribute()

## 作用

1. 获取web.xml中配置的上下文参数
2. 获取当前的工程路径，格式：/工程名
3. 获取工程部署后在服务器磁盘上的绝对路径
4. 像Map一样存取数据

```java
public class HelloServlet4 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 获取context-param
        ServletContext servletContext = getServletConfig().getServletContext();
        System.out.printf("param-context username: " + servletContext.getInitParameter("username"));
        System.out.printf("param-context password: " + servletContext.getInitParameter("password"));
        // 获取工程路径
        System.out.println("工程路径：" + servletContext.getContextPath());
        // 获取工程部署在服务器硬盘的绝对路径
        System.out.println("绝对路径：" + servletContext.getRealPath("/"));
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }
}
```

```java
		<context-param>
        <param-name>username</param-name>
        <param-value>password</param-value>
    </context-param>

		<servlet>
        <servlet-name>HelloServlet4</servlet-name>
        <servlet-class>com.hqw.test1.HelloServlet4</servlet-class>
    </servlet>
		<servlet-mapping>
        <servlet-name>HelloServlet4</servlet-name>
        <url-pattern>/hello4</url-pattern>
    </servlet-mapping>
```
输出：

param-context username: passwordparam-context password: null工程路径：/Tomcat\_test1

绝对路径：C:\Users\hqw\IdeaProjects\webtest\test1\target\test1-1.0-SNAPSHOT\

```java
public class ServletContext1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext servletContext = getServletContext();
        System.out.println("保存前：" + servletContext.getAttribute("key"));  //null
        servletContext.setAttribute("key", "Yeah!");
        System.out.println("保存后：" + servletContext.getAttribute("key"));  // Yeah!
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }
}
```
如果在此工程下另一个servletContext对象getAttribute时会直接得到Yeah。因为ServletContext是在工程部署启动时创建，在web工程停止时销毁。

# HttpServletRequest类

## 作用

每次只要有请求进入Tomcat服务器，Tomcat就会把请求过来的协议信息解析封装到Request对象中。然后传递到service方法（doGet，doPost）

## 方法

`getReauestURI()` 获取请求资源路径

`getRequestURL()` 获取统一资源定位符（绝对路径）

`getRemoteHost()` 获取客户端IP地址

`getParameter()` 获取请求参数

`getParameterValues()` 获取多参数请求

`getMethod()` 获取请求的方式GET或POST

`setAttribute(key)` 获取域数据

`getRequestDispatcher`

```java
//获取客户端请求url
StringBuffer requestURL = req.getRequestURL();
//获取客户端请求项目中的具体资源
String requestURI = req.getRequestURI();
// 获取客户端发送请求时的端口
int serverPort = req.getServerPort();
// 获取本应用在容器的端口
int localPort = req.getLocalPort();
// 获取客户端程序的端口
int remotePort = req.getRemotePort();
// 获取请求协议
String scheme = req.getScheme();
// 获取协议及版本号
String protocol = req.getProtocol();
// 获取请求方式
String method = req.getMethod();
```

```java
// 获取头名字
Iterator<String> iterator = req.getHeaderNames().asIterator();
while (iterator.hasNext()) {
    String next = iterator.next();
    System.out.println(next);
}
//根据头名获取请求头
System.out.println(req.getHeader("connection"));
// 获取content-type
System.out.println(req.getContentType());
```

```java
// 获取指定参数
String name = req.getParameter("name");
// 获取参数值数组
String[] names = req.getParameterValues("name");
// 获取参数名
Enumeration<String> parameterNames = req.getParameterNames();
// 获取请求参数的键值对
Map<String, String[]> parameterMap = req.getParameterMap();
// 获取请求体字符输入流
BufferedReader reader = req.getReader();
// 获取字节输入流
ServletInputStream inputStream = req.getInputStream();
// 请求体长度
int contentLength = req.getContentLength();
```

```java
String servletPath = req.getServletPath();
// 获取ServletContext对象
ServletContext servletContext = req.getServletContext();
// 获取请求中的所有cookie
Cookie[] cookies = req.getCookies();
// 获取session对象
HttpSession session = req.getSession();
// 设置请求体字符集
req.setCharacterEncoding("UTF-8");
```
**基本请求**

```java
public class RequestAPIServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("URI: " + req.getRequestURI());
        System.out.println("URL: " + req.getRequestURL());
        System.out.println("client IP: " + req.getRemoteHost());
        System.out.println("请求头User-Agent: " + req.getHeader("User-Agent"));
        System.out.println("请求方式: " + req.getMethod());
        // 获取请求参数
        System.out.println("请求用户名: " + req.getParameter("username")); //请求用户名: 123
        System.out.println("请求密码: " + req.getParameter("password")); //请求密码: 12345
        System.out.println("请求爱好: " + req.getParameter("hobby")); //只有一个值时 请求爱好: cpp
        System.out.println("请求爱好: " + Arrays.toString(req.getParameterValues("hobby"))); //有多个参数时 请求爱好: [cpp, java, js]
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 设置请求体字符集为UTF-8, 从而解决post请求中文乱码的问题, 要在获取请求参数之前调用
        req.setCharacterEncoding("UTF-8");
    }
}

```

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="http://localhost:8080/Tomcat_test1/requestAPIServlet" method="get">
        用户名: <input type="text" name="username"><br/>
        密码: <input type="password" name="password"><br/>
        兴趣爱好: <input type="checkbox" name="hobby" value="cpp">C++
        <input type="checkbox" name="hobby" value="java">Java
        <input type="checkbox" name="hobby" value="js">JavaScript<br/>
        <input type="submit">
    </form>
</body>
</html>
```
# HttpServletResponse类

## 作用

和request一样，请求进来，Tomcat服务器创建一个Response对象传递给Servlet程序去使用。

## 方法

```java
// 设置响应码
resp.setStatus(200);
// 设置响应头
resp.setHeader("user-agent", "xxx");
// 设置content-type(MIME)
resp.setContentType("txt/html");
// 获得响应体放入信息的字符输出流
PrintWriter writer = resp.getWriter();
// 获得响应体放入信息的字节输出流
ServletOutputStream outputStream = resp.getOutputStream();
// 设置响应体字节长度
resp.setContentLength(255);

// 错误响应
resp.sendError(503, "xxxx");
resp.addCookie(new Cookie("xx", "oo"));
resp.setCharacterEncoding("utf-8");
```
## 两个输出流

1. 子节流 `getOutputStream()` 常用于下载（传递二进制数据）
2. 字符流 `getWriter()` 常用于字符串回传

两个流只能同时用一个

```java
public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置服务器字符集
        response.setCharacterEncoding("UTF-8");
        // 通过响应头，设置浏览器也使用UTF-8字符集
        response.setHeader("Content-Type", "text/html; charset=UTF-8");
        // 这种方式可以同时设置服务器和客户端都使用UTF-8字符集，还设置了响应头
        // response.setContentType("text/html; charset=UTF-8");
        // 回传字符串数据
        PrintWriter writer = response.getWriter();
        writer.write("response content 嗨");
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }
}
```
# 请求转发与重定向

## 请求转发

* 服务器收到请求后，从一个资源跳转到另一个资源。
* 如果Servlet1和2同时完成一个业务，那么就需要1转发到2
* 通过HttpServletRequest获取请求转发器

```java
public class RequestServlet1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 请求转发
        req.setAttribute("key", "1盖章");
        // 问路
        //请求转发必须要以斜杠打头, 映射到web目录
        RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");
        // 走向Servlet2
        requestDispatcher.forward(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    }
}
```

```java
public class RequestServlet2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		System.out.println("请求用户名: " + req.getParameter("username"));
        Object key1 = req.getAttribute("key1");
        System.out.println("柜台1是否有章: " + key1);
        //处理自己的业务
    }
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    }
}
```
通过访问servlet1: URL?username=tom 访问1并转发到2

特点：

1. **浏览器地址没有变化**
2. **他们是一次请求，是服务器内部行为，对客户端是屏蔽的**
3. 他们共享Request域中的数据
4. 可以转发到WEB-INF目录下
5. 是否可以访问工程以外的资源 NO

## 请求重定向

* 客户端给服务器发请求，服务器告诉客户端新的地址去访问
* 客户端至少发送了两次请求，地址栏会变化
* 通过HttpServletResponse对象的sendRedirect方法实现

![](Servlet_002.png)

```java
class Response1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("Response1");
        resp.setStatus(302);
        // 设置响应头
        resp.setHeader("Location", "http://localhost:8080/servlet/response2");

        // 更简单的方法 不用设置响应码
        resp.sendRedirect("http://localhost:8080/servlet/response2")
    }
}
```

```java
class Response2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		resp.getWriter().write("response2 result");
    }
}
```
特点：

1. **浏览器地址栏会变化**
2. **两次请求**
3. **不共享Request域中的数据**
4. **不能访问WEB-INF下的资源**
5. **可以访问当前工程以外的网站**

base标签可以设置当前页面中所有相对路径工作时，参照哪个路径来进行跳转

`<base href="http://localhost:8080/a/b/">`

在web中，/是一种绝对路径

1. 斜杠如果被浏览器解析，得到的地址是：http://ip:port/
<a href="/">斜杠</a>
2. 如果被服务器解析，得到的地址是：http://ip:port/工程路径
1. <url-parttern>/servlet1</url-parttern>
2. servletContext.getRealPath("/");
3. request.getRequestDispatcher("/")
3. 特殊情况：请求重定向
1. response.sendRediect("/") 把斜杠发给浏览器解析得到 http://ip:port/

# Servlet原理

![](Servlet_003.jpg)## base标签

## 斜杠的含义

# Mapping问题

1. 一个Servlet可以指定一个映射路径

```java
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello1</url-pattern>
  </servlet-mapping>
```
2. 一个Servlet可以指定多个映射路径

```java
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello1</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello2</url-pattern>
  </servlet-mapping>
```
3. 一个Servlet可以指定通用映射路径

```java
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello/*</url-pattern>
  </servlet-mapping>
```
4. 默认请求

```java
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/*</url-pattern>
  </servlet-mapping>
```
5. 指定一些后缀或者前缀

```java
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>*.sagiri</url-pattern>
  </servlet-mapping>
```
6. 优先级

指定了固有路径的优先级最高，如果找不到就走默认的处理请求

