ASP:

* 微软：ASP
* HTML中嵌入VB脚本：ASP+COM
* 在ASP开发中，基本界面

php

* PHP开发速度快，功能强大，代码简单
* 无法承载大访问量

JSP/Servlet:

* B/S浏览器和服务器
* C/S客户端和服务器
* sun公司主推BS架构
* 基于Java语言
* 可以承载三高问题影响
* 语法像ASP

服务器是一种被动的操作，用来处理用户请求给出响应

### IIS

微软，ASP Windows自带

## Tomcat

...Apache

实际运行Servlet JSP

启动：bin/startup.sh

结束：bin/shutdown.sh

#### 更改配置

tomcat/conf/server.xml

Windows系统ip配置c:\windows\system32\drivers\etc\hosts

Mac /etc/hosts

```
# 默认主机名称            网站应用存放位置
<Host name="localhost"  appBase="webapps"
      unpackWARs="true" autoDeploy="true">

  # 默认端口
<Connector port="8080" protocol="HTTP/1.1"
     connectionTimeout="20000"
     redirectPort="8443" />
```
#### 网站如何访问

1. 输入域名
2. 检查本机hosts配置文件下有没有域名映射
3. 没有的话去DNS服务器中找

#### 发布web网站

* 将自己写的网站，放到Tomcat中制定的web应用文件夹下(webapps)下，就可以访问了

网站应该有结构

--webapps: Tomcat服务器web目录

--ROOT

--kuangstudy：网站目录名

 -- WEB-INF：

     -- classes: java程序

 -- lib : web应用依赖的包

 -- web.xml 网站配置

-- index.html 默认首页

-- static

-css

-style.css

-js

-img

