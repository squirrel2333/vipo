
**步骤：**

1. 服务端在为客户创建session时，会同时将session对象的id，即JSEESIONID以Cookie的形式放入响应对象
2. 后端创建完Session后，客户端会收到一个名为JSESSIONID的Cookie
3. 客户端下次请求时携带该JSESSIONID，后端收到后根据JSESSIONID找到对应的SESSION对象

**特点：**

* Session是一个接口
* Session用来维护客户端和服务器的关联
* 每个客户端都有自己的一个Session会话
* Session会话中，我们经常用来保存登录之后的信息
* 保存在服务器端

**场景：**

1. 记录用户登陆信息
2. 记录用户操作历史

# 如何创建Session和获取

创建和获取Session的API是一样的，第一次调用时创建Session，之后调用是获取创建好的Session

`request.getSession()`

判断是否是新创建的Session

`isNew()`true表示刚创建

每个会话都有一个身份证号，也就是ID值，且唯一

`getId()`得到ID

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="pragma" content="no-cache" />
<meta http-equiv="cache-control" content="no-cache" />
<meta http-equiv="Expires" content="0" />
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Session</title>
<style type="text/css">

	ul li {
		list-style: none;
	}

</style>
</head>
<body>
	<iframe name="target" width="500" height="500" style="float: left;"></iframe>
	<div style="float: left;">
		<ul>
			<li><a href="http://localhost:8080/session/sessionServlet?action=createOrGetSession" target="target">Session的创建和获取（id号、是否为新创建）</a></li>
			<li><a href="http://localhost:8080/session/sessionServlet?action=setAttribute" target="target">Session域数据的存储</a></li>
			<li><a href="http://localhost:8080/session/sessionServlet?action=getAttribute" target="target">Session域数据的获取</a></li>
			<li>Session的存活</li>
			<li>
				<ul>
					<li><a href="" target="target">Session的默认超时及配置</a></li>
					<li><a href="" target="target">Session3秒超时销毁</a></li>
					<li><a href="" target="target">Session马上销毁</a></li>
				</ul>
			</li>
			<li><a href="" target="target">浏览器和Session绑定的原理</a></li>
		</ul>
	</div>
</body>
</html>
```

```
public class SessionServlet extends BaseServlet {

    protected void createOrGetSession(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 创建获取Session对象
        HttpSession session = request.getSession();
        // 判断是否是新创建的
        boolean isNew = session.isNew();
        // 获取Session的唯一标识
        String jSessionId = session.getId();

        response.getWriter().write("Session id 是：" + jSessionId + "<br />");
        response.getWriter().write("是否是新创建的：" + isNew + "<br />");
    }
}
```
# Session域数据的存取

`session.setAttribute()`

`seesion.getAttribute()`

`session.removeAttribute()`

```
public class SessionServlet extends BaseServlet {

    protected void setAttribute(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{
        request.getSession().setAttribute("key1", "value");
    }

    protected void getAttribute(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Session域数据的获取
        Object key1 = request.getSession().getAttribute("key1");
        response.getWriter().write("从session中获取的key1：" + key1);
    }
}
```
* **session的作用域是整个客户端，即使不同的请求也共享session.attribute**

![](Session会话_001.png)

# Session生命周期控制

```
protected void defaultLife(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{
   int maxInactiveInterval = request.getSession().getMaxInactiveInterval();
   response.getWriter().write("Session默认超时时长为：" + maxInactiveInterval + "秒"); // 1800s 30 分钟
}
```
如果希望web工程Session超时时长为其他时长，可以在web.xml做配置

```
<session-config>
  <session-timeout>20</session-timeout>
</session-config>
```
如果只想修改个别Session

```
protected void setSessionLife(HttpServletRequest request, HttpServletResponse response) {
    // 设置3秒超时
    request.getSession().setMaxInactiveInterval(3);
}
```
Session超时指的是两次请求最大的间隔时长，如果一直连接，则不会超时

`session.invalidate()`让session立即失效

# Session和浏览器的关联

Session技术底层基于Cookie技术来实现的

![](Session会话_002.png)

