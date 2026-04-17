
1. javaweb三大组件之一
1. JavaEE的规范，一种接口
2. 作用是监听某种事物变化。通过回调函数，反馈给程序处理

# application域监听器

## ServletContextListener监听器

ServletContextListener他可以监听ServletContext对象的创建和销毁。

ServletContext对象在web工程启动时创建，停止时销毁

监听到创建和销毁之后 都会调用ServletContextListener监听器的方法反馈

```java
public interface ServletContextListener extends EventListener {
    // ServletContext创建之后马上调用
    public void contextInitalized(ServletContextEvent sce);
    // ServletContext对象销毁后调用
    public void contextDestroyed(ServldtContextEvent sce);
}
```
ServletContextEvent对象代表从ServletContext对象上捕获到的事件，通过这个事件对象我们可以获取到ServletContext对象

## ServletContextAttributeListener

监听ServletContext中属性的添加修改移除

![](Listener监听器_001.png)

## 测试

### 定义监听器

![](Listener监听器_002.png)![](Listener监听器_003.png)

### 触发监听器

![](Listener监听器_004.png)

# session域监听器

## HttpSessionListener

监听HttpSession对象的创建与销毁

* `sessionCreated(HttpSessionEvent hse)`对象创建时调用
* `sessionDestroyed(HttpSessionEvent hse)`HttpSession对象销毁时调用

HttpSessionEvent对象代表从HttpSession对象上捕获到的事件，通过这个事件对象我们可以获取到触发事件的HttpSession对象。

## HttpSessionAttributeListener

监听HttpSession中属性的添加移除和修改

![](Listener监听器_005.png)

## 测试

### 定义监听器

![](Listener监听器_006.png)

![](Listener监听器_007.png)

### 触发监听器

![](Listener监听器_008.png)

# request域监听器

## ServletRequestListener

监听ServletRequest对象的创建与销毁

* `requestInitialized(ServletRequestEvent sre)`ServletRequest对象创建时调用
* `requestDestroyed(ServletRequestEvent sre)`ServletRequest对象销毁时调用

ServletRequestEvent对象代表从HttpServletRequest对象上捕获到的事件，通过这个事件对象我们可以获取到触发事件的HttpServletRequest对象。另外有一个方法可以获得到当前Web应用的ServletContext对象。

## ServletRequestAttributeListener

![](Listener监听器_009.png)

## 测试

### 定义监听器

![](Listener监听器_010.png)

![](Listener监听器_011.png)

### 触发监听器

![](Listener监听器_012.png)

# session域的两个特殊监听器

## session绑定监听器 HttpSessionBindingListener

监听当前监听器对象在Session域中的增加与移除

![](Listener监听器_013.png)

![](Listener监听器_014.png)

![](Listener监听器_015.png)

![](Listener监听器_016.png)

## 钝化活化监听器 HttpSessionActivationListener

监听某个对象在Session中的序列化与反序列化

* `sessionWillPassivate(HttpSessionEvent se)`该类实例和Session一起钝化到硬盘时调用
* `sessionDidActivate(HttpSessionEvent se)`该类实例和Session一起活化到内存时调用

![](Listener监听器_017.png)

![](Listener监听器_018.png)

* 请求ServletB获取session，获取重启前存入的数据

![](Listener监听器_019.png)

![](Listener监听器_020.png)

![](Listener监听器_021.png)

