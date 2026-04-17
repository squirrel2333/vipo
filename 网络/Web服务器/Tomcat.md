
# 基础

## 目录介绍

* bin： 可执行程序
* conf： 配置文件
* lib：jar包
* log：运行输出的日志
* temp：存放临时数据
* webapps： 部署的web工程
* work： 工作目录，存放运行时jsp翻译为servlet的源码，和session纯化目录

## 如何启动

`bin/startup.sh` / `startup.bat`

或`bin/catalina.sh run` 这种方法可以看到信息

## 修改端口号

conf/server.xml -> 修改port 属性 -> 重启tomcat

http默认端口号80

## 如何部署web工程到Tomcat中

### 方法1：

1. 只需把web工程拷贝到Tomcat的webapps目录下
2. 比如webapps/book/pages/regist.html文件，只需要在url加上对应的路径: book/pages/regist.html

### 方法2：

1. 找到tomcat/conf/Catalina/localhost/新建一个abc.xml
2.

```
<!-- Context 表示工程上下文 path表示工程的访问路径：/abc docBase表示你的工程目录在哪里 --!>
<Context path="/abc" docBase="E:\book" />
```
3. 此时访问 http://localhost:8080/abc/index.html就表示访问E盘下工程目录中的index

## ROOT的工程访问，以及默认index.html页面的访问

root 是 webapp 下面的工程文件夹例如 book

当输入地址为http://ip:host/ -> 没有工程名，访问的是ROOT文件夹工程下的index

当我们访问的是http://ip:host/工程名/ -->没有资源名，访问的是工程下的index

## IDEA整合

### 创建web工程

setting -> build -> Application Server -> + ->

new module -> java enterprise

* web
+ src 存放源码
+ web 存放web工程资源
- WEB-INF 受服务器保护的目录 浏览器无法直接访问到此目录的内容
* lib目录 存放第三方jar包（还需要自己配置导包）
* web.xml 他是整个web工程的配置部署描述文件，可以配置很多组件例如：Sevlet Filter Listener...

### 部署到Tomcat上运行

`http://localhost:8080/baidu/demo01.html`baidu -> Context root, demo1.html -> URI

1. 建议修改web工程对应的Tomcat运行实例名称 edit configurations
2. Deployment 里增加删除需要部署到web工程，添加到tomcat实例中。Application context是工程路径
3. server中运行实例默认的访问地址
4. 运行/debug和停止tomcat

![](Tomcat_001.png)

![](Tomcat_002.png)

