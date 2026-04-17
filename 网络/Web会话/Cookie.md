
# 什么是Cookie

1. Cookie 是服务器通知客户端保存键值对的一种技术
2. 客户端有了Cookie后，每次请求都携带发送给服务器
3. 每个Cookie不能大于4kb
4. Cookie存储于客户端，存放不敏感的数据

# 如何创建Cookie

![](Cookie_001.png)

```
public class CookieServlet extends BaseServlet{

    protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 创建Cookie对象
        Cookie cookie = new Cookie("key1", "value1");
        // 通知客户端保存cookie
        resp.addCookie(cookie);
        resp.getWriter().write("Cookie创建成功");
    }
}
```
# 获取Cookie

```
   protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        Cookie[] cookies = req.getCookies();

        // 寻找需要的cookie，可以专门封装到一个类中
        Cookie wantCookie = null;
        for (Cookie cookie : cookies) {
            if ("key2".equals(cookie.getName())) {
                wantCookie = cookie;
                break;
            }
        }
        if (wantCookie != null){
            // getName返回Cookie的key getValue返回值
            resp.getWriter().write("Cookie[" + wantCookie.getName() + "]" + "=" + wantCookie.getValue());
        }
    }
```
# 更新Cookie

```
// 方案一
protected void updateCookie(HttpServletRequest req, HttpServletResponse resp) {
        // 构造器中赋予新的Cookie值
        Cookie cookie = new Cookie("key1", "newValue1");
        // 通知客户端保存修改
        resp.addCookie(cookie);
}

// 方案二
protected void updateCookie2(HttpServletRequest req, HttpServletResponse resp) {
    Cookie newCookie = CookieUtile.findCookie("key2", req.getCookies());
    if (newCookie != null) {
        newCookie.setValue("newValue");
        resp.addCookie(newCookie);
    }
}

```
# Cookie的时效性

会话级：

* 服务器不明确存在时间
* 浏览器端，cookie存在内存
* 浏览器关闭，cookie会被释放

持久化级：

* 服务端设置cookie存在时间
* 浏览器端cookie会被保存在硬盘
* 存在时间不受浏览器关闭影响，根据预设时间决定

**cookie.setMaxAge(int expiry)参数单位是秒，表示cookie的持久化时间，如果设􏰿􏲅数为0，表示将浏览器中保存的的该cookie删除。**

**如果不设超时时间，默认为会话级的Cookie**

```
@WebServlet(name = "helloServlet5", urlPatterns = "/hello5")
public class HelloServlet5 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Cookie[] cookies = req.getCookies();
        Cookie wantedCookie = null;
        if (cookies != null && cookies.length != 0) {
            for (Cookie cookie: cookies) {
                if ("key".equals(cookie.getName())) {
                    wantedCookie = cookie;
                }
            }
        }
        if (wantedCookie != null) {
            resp.getWriter().write(wantedCookie.getName() + ":" + wantedCookie.getValue());
        } else {
            Cookie newCookie = new Cookie("key", String.valueOf(new Random().nextLong()));
            newCookie.setMaxAge(60);
            resp.addCookie(newCookie);
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```
# Cookie的提交路径

访问资源时不需要每次把所有的Cookie带上。访问不同资源时，可以携带不同的cookie，可以通过setPath(String path)对Cookie的路径进行设计

```
// 只有uri为这个路径，才会携带这个cookie
newCookie.setPath("/demo01/hello5");
```

