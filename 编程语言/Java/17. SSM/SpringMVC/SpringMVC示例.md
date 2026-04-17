
# 开发环境

IDE：idea 2019.2

构建工具：maven3.5.4

服务器：tomcat7

Spring版本：5.3.1

# 创建maven工程

##### a>添加web模块

##### b>打包方式：war <packaging>war</packaging>

##### c>引入依赖

```java
<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>

    <!-- 日志 -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>

    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>

    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>
</dependencies>
```
注：由于 Maven 的传递性，我们不必将所有需要的包全部配置依赖，而是配置最顶端的依赖，其他靠传递性导入。Spring-webmvc已经包含了spring-core等核心包

# 配置web.xml

注册SpringMVC的前端控制器DispatcherServlet

##### a>默认配置方式web

此配置作用下，SpringMVC的配置文件(不是web配置文件，是spirng配置文件)默认位于WEB-INF下，默认名称为<servlet-name>-servlet.xml，例如，以下配置所对应SpringMVC的配置文件位于WEB-INF下，文件名为springMVC-servlet.xml

```java
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
        设置springMVC的核心控制器所能处理的请求的请求路径
        /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
        但是/不能匹配.jsp请求路径的请求
      /*代表所有文件
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```
##### b>扩展配置方式

可通过init-param标签设置SpringMVC配置文件的位置和名称（maven工程最好放在resources下面），通过load-on-startup标签设置SpringMVC前端控制器DispatcherServlet的初始化时间

```java
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
    <init-param>
        <!-- contextConfigLocation为固定值 -->
        <param-name>contextConfigLocation</param-name>
        <!-- 使用classpath:表示从类路径查找配置文件，例如maven工程中的src/main/resources -->
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!--
 		作为框架的核心组件，在启动过程中有大量的初始化操作要做
		而这些操作放在第一次请求时才执行会严重影响访问速度
		因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时
	-->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
        设置springMVC的核心控制器所能处理的请求的请求路径
        /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
        但是/不能匹配.jsp请求路径的请求
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```
注：

 <url-pattern>标签中使用/和/\*的区别：

/所匹配的请求可以是/login或.html或.js或.css方式的请求路径，但是/不能匹配.jsp请求路径的请求

 因此就可以避免在访问jsp页面时，该请求被DispatcherServlet处理，从而找不到相应的页面

 /\*则能够匹配所有请求，例如在使用过滤器时，若需要对所有请求进行过滤，就需要使用/\*的写法

路径解析分为浏览器解析和服务器解析

* 浏览器解析，例如超链接<a href="/target"> 代表localhost:8080/
* 服务器解析，例如<url-pattern>/<url=pattern> 代表工程路径Application context/ 例如![](SpringMVC示例_001.png)则/代表了webapp/ 浏览器访问路径为http://localhost:8080/springMVC/
# 创建请求控制器

由于前端控制器对浏览器发送的请求进行了统一的处理，但是具体的请求有不同的处理过程，因此需要创建处理具体请求的类，即请求控制器

请求控制器中每一个处理请求的方法成为控制器方法

因为SpringMVC的控制器由一个POJO（普通的Java类）担任，因此需要通过@Controller注解将其标识为一个控制层组件，交给Spring的IoC容器管理，此时SpringMVC才能够识别控制器的存在

```java
@Controller
public class HelloController {

}
```
# 创建springMVC的配置文件

```java
<!-- 自动扫描包 -->
<context:component-scan base-package="com.atguigu.mvc.controller"/>

<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">

                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>

                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8" />
                </bean>
            </property>
        </bean>
    </property>
</bean>

<!--
   处理静态资源，例如html、js、css、jpg
  若只设置该标签，则只能访问静态资源，其他请求则无法访问
  此时必须设置<mvc:annotation-driven/>解决问题
 -->
<mvc:default-servlet-handler/>

<!-- 开启mvc注解驱动 -->
<mvc:annotation-driven>
    <mvc:message-converters>
        <!-- 处理响应中文内容乱码 -->
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="defaultCharset" value="UTF-8" />
            <property name="supportedMediaTypes">
                <list>
                    <value>text/html</value>
                    <value>application/json</value>
                </list>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```
# 测试HelloWorld

![](SpringMVC示例_002.png)

##### a>实现对首页的访问

```java
// @RequestMapping注解：处理请求和控制器方法之间的映射关系
// @RequestMapping注解的value属性可以通过请求地址匹配请求，/表示的当前工程的上下文路径
@RequestMapping("/")
public String index() {
    //设置视图名称
    return "index";
}
```
##### b>通过超链接跳转到指定页面

```java
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
    <h1>首页</h1>
    <a th:href="@{/target}">HelloWorld</a><br/>
    <!--自动帮我们把绝对路径加上上下文路径-->
</body>
</html>
```

```java
@RequestMapping("/target")
public String toTarget() {
    return "target";
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
    HelloWorld
</body>
</html>
```
# 总结

1. 访问`http://localhost/springMVC/` (`/springMVC`是设置的application context)
2. web.xml中的servlet读取到`/`映射到前端控制器`DispatcherServlet`处理。
3. 前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器
4. 将请求地址和控制器中`@RequestMapping`注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。
5. 处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过`Thymeleaf`对视图进行渲染，最终转发到视图所对应页面，即能够访问WEB-INF下的`index.html`资源(本质是转发，重定向无法访问WEB-INF下）
6. `index.html`中包含thymeleaf命名空间，解析链接为上下文加上链接内容进行访问，即`/target`
7. 重复上述步骤通过`toTarget`方法返回视图名称给视图解析器再访问`target.html`
