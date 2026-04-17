# RESTful简介

REST：**Re**presentational **S**tate **T**ransfer，表现层资源状态转移。

## 资源

资源是一种看待服务器的方式，即，将服务器看作是由很多离散的资源组成。每个资源是服务器上一个可命名的抽象概念。因为资源是一个抽象的概念，所以它不仅仅能代表服务器文件系统中的一个文件、数据库中的一张表等等具体的东西，可以将资源设计的要多抽象有多抽象，只要想象力允许而且客户端应用开发者能够理解。与面向对象设计类似，资源是以名词为核心来组织的，首先关注的是名词。一个资源可以由一个或多个URI来标识。URI既是资源的名称，也是资源在Web上的地址。对某个资源感兴趣的客户端应用，可以通过资源的URI与其进行交互。

## 资源的表述

资源的表述是一段对于资源在某个特定时刻的状态的描述。可以在客户端-服务器端之间转移（交换）。资源的表述可以有多种格式，例如HTML/XML/JSON/纯文本/图片/视频/音频等等。资源的表述格式可以通过协商机制来确定。请求-响应方向的表述通常使用不同的格式。

## 状态转移

状态转移说的是：在客户端和服务器端之间转移（transfer）代表资源状态的表述。通过转移和操作资源的表述，来间接实现操作资源的目的。

# RESTful的实现

具体说，就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。

它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源。

REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性。

|  |  |  |
| --- | --- | --- |
| 操作 | 传统方式 | REST风格 |
| 查询操作 | getUserById?id=1 | user/1-->get请求方式 |
| 保存操作 | saveUser | user-->post请求方式 |
| 删除操作 | deleteUser?id=1 | user/1-->delete请求方式 |
| 更新操作 | updateUser | user-->put请求方式 |

```java
<mvc:view-controller path="/test_rest" view-name="test_rest"></mvc:view-controller>
<mvc:annotation-driven></mvc:annotation-driven>
```

```java

@Controller
public class UserController {

    @RequestMapping(value = "/users", method = RequestMethod.GET)
    public String getAllUsers() {
        System.out.println("查询所有用户");
        return "success";
    }

    @RequestMapping(value = "/user/{id}", method = RequestMethod.GET)
    public String getUserById() {
        System.out.println("根据ID查询用信息");
        return "success";
    }

    @RequestMapping(value = "/user", method = RequestMethod.POST)
    public String inserUser(String username, String password) {
        System.out.println("添加用户信息" + username + " " + password);
        return "success";
    }

}
```

```java
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <a th:href="@{/user}">查询所有用户信息</a>
    <a th:href="@{/user/1}">查询指定用户信息</a>
    <form th:action="@{/user}" method="post">
        用户名: <input type="text" name="username"><br>
        密码:  <input type="password" name="password"><br>
        <input type="submit" value="add">
    </form>

</body>
</html>
```
## RESTFul风格接口设计

1. **接口设计**

|  |  |  |  |
| --- | --- | --- | --- |
|  |  |  |  |
| 功能 | 接口和请求方式 | 请求参数 | 返回值 |
| 分页查询 | GET  /user | page=1&size=10 | { 响应数据 } |
| 用户添加 | POST /user | { user 数据 } | {响应数据} |
| 用户详情 | GET /user/1 | 路径参数 | {响应数据} |
| 用户更新 | PUT /user | { user 更新数据} | {响应数据} |
| 用户删除 | DELETE /user/1 | 路径参数 | {响应数据} |
| 条件模糊 | GET /user/search | page=1&size=10&keywork=关键字 | {响应数据} |

2. **问题讨论**
为什么查询用户详情，就使用路径传递参数，多条件模糊查询，就使用请求参数传递？
误区：restful风格下，不是所有请求参数都是路径传递！可以使用其他方式传递！
在 RESTful API 的设计中，路径和请求参数和请求体都是用来向服务器传递信息的方式。
+ 对于查询用户详情，使用路径传递参数是因为这是一个单一资源的查询，即查询一条用户记录。使用路径参数可以明确指定所请求的资源，便于服务器定位并返回对应的资源，也符合 RESTful 风格的要求。
+ 而对于多条件模糊查询，使用请求参数传递参数是因为这是一个资源集合的查询，即查询多条用户记录。使用请求参数可以通过组合不同参数来限制查询结果，路径参数的组合和排列可能会很多，不如使用请求参数更加灵活和简洁。

此外，还有一些通用的原则可以遵循：

+ 路径参数应该用于指定资源的唯一标识或者 ID，而请求参数应该用于指定查询条件或者操作参数。
+ 请求参数应该限制在 10 个以内，过多的请求参数可能导致接口难以维护和使用。
+ 对于敏感信息，最好使用 POST 和请求体来传递参数。

# HiddenHttpMethodFilter

由于浏览器只支持发送get和post方式的请求，那么该如何发送put和delete请求呢？

SpringMVC 提供了 **HiddenHttpMethodFilter** 帮助我们**将 POST 请求转换为 DELETE 或 PUT 请求**

**HiddenHttpMethodFilter** 处理put和delete请求的条件：

a>当前请求的请求方式必须为post

b>当前请求必须传输请求参数\_method

满足以上条件，**HiddenHttpMethodFilter** 过滤器就会将当前请求的请求方式转换为请求参数\_method的值，因此请求参数\_method的值才是最终的请求方式

```java
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        HttpServletRequest requestToUse = request;
        if ("POST".equals(request.getMethod()) && request.getAttribute("javax.servlet.error.exception") == null) {
            String paramValue = request.getParameter(this.methodParam);
            if (StringUtils.hasLength(paramValue)) {
                String method = paramValue.toUpperCase(Locale.ENGLISH);
                if (ALLOWED_METHODS.contains(method)) {
                    requestToUse = new HttpMethodRequestWrapper(request, method);
                }
            }
        }

        filterChain.doFilter((ServletRequest)requestToUse, response);
    }

    static {
        ALLOWED_METHODS = Collections.unmodifiableList(Arrays.asList(HttpMethod.PUT.name(), HttpMethod.DELETE.name(), HttpMethod.PATCH.name()));
    }

    private static class HttpMethodRequestWrapper extends HttpServletRequestWrapper {
        private final String method;

        public HttpMethodRequestWrapper(HttpServletRequest request, String method) {
            super(request);
            this.method = method;
        }

        public String getMethod() {
            return this.method;
        }
    }
```

```java
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

```java
    <form th:action="@{/user}" method="post">
        <input type="hidden" name="_method" value="PUT">
        用户名: <input type="text" name="username"><br>
        密码:  <input type="password" name="password"><br>
        <input type="submit" value="add">
    </form>
```
注：

 目前为止，SpringMVC中提供了两个过滤器：CharacterEncodingFilter和HiddenHttpMethodFilter

在web.xml中注册时，必须先注册CharacterEncodingFilter，再注册HiddenHttpMethodFilter

 原因：

* 在 CharacterEncodingFilter 中通过 request.setCharacterEncoding(encoding) 方法设置字符集的
* request.setCharacterEncoding(encoding) 方法要求前面不能有任何获取请求参数的操作
* 而 HiddenHttpMethodFilter 恰恰有一个获取请求方式的操作：
* `String paramValue = request.getParameter(this.methodParam)`
# RESTful示例：查询所有员工数据

## 实体类

```java
public class Employee {

     private Integer id;
     private String lastName;

     private String email;
     //1 male, 0 female
     private Integer gender;

     public Integer getId() {
        return id;
     }

     public void setId(Integer id) {
        this.id = id;
     }

     public String getLastName() {
        return lastName;
     }

     public void setLastName(String lastName) {
        this.lastName = lastName;
     }

     public String getEmail() {
        return email;
     }

     public void setEmail(String email) {
        this.email = email;
     }

     public Integer getGender() {
        return gender;
     }

     public void setGender(Integer gender) {
        this.gender = gender;
     }

     public Employee(Integer id, String lastName, String email, Integer gender) {
        super();
        this.id = id;
        this.lastName = lastName;
        this.email = email;
        this.gender = gender;
     }

     public Employee() {
     }
  }
```

```java
  import java.util.Collection;
  import java.util.HashMap;
  import java.util.Map;

  import com.atguigu.mvc.bean.Employee;
  import org.springframework.stereotype.Repository;

  @Repository
  public class EmployeeDao {

     private static Map<Integer, Employee> employees = null;

     static{
        employees = new HashMap<Integer, Employee>();

        employees.put(1001, new Employee(1001, "E-AA", "aa@163.com", 1));
        employees.put(1002, new Employee(1002, "E-BB", "bb@163.com", 1));
        employees.put(1003, new Employee(1003, "E-CC", "cc@163.com", 0));
        employees.put(1004, new Employee(1004, "E-DD", "dd@163.com", 0));
        employees.put(1005, new Employee(1005, "E-EE", "ee@163.com", 1));
     }

     private static Integer initId = 1006;

     public void save(Employee employee){
        if(employee.getId() == null){
           employee.setId(initId++);
        }
        employees.put(employee.getId(), employee);
     }

     public Collection<Employee> getAll(){
        return employees.values();
     }

     public Employee get(Integer id){
        return employees.get(id);
     }

     public void delete(Integer id){
        employees.remove(id);
     }
  }
```
## 功能

|  |  |  |
| --- | --- | --- |
| 功能 | URL 地址 | 请求方式 |
| 访问首页√ | / | GET |
| 查询全部数据√ | /employee | GET |
| 删除√ | /employee/2 | DELETE |
| 跳转到添加数据页面√ | /toAdd | GET |
| 执行保存√ | /employee | POST |
| 跳转到更新数据页面√ | /employee/2 | GET |
| 执行更新√ | /employee | PUT |

## 访问首页

##### a>配置view-controller

```java
<mvc:view-controller path="/" view-name="index"/>
```
##### b>创建页面

```java
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8" >
    <title>Title</title>
</head>
<body>
<h1>首页</h1>
<a th:href="@{/employee}">访问员工信息</a>
</body>
</html>
```
## 查询所有员工数据

##### a>控制器方法

```java
@RequestMapping(value = "/employee", method = RequestMethod.GET)
public String getEmployeeList(Model model){
    Collection<Employee> employeeList = employeeDao.getAll();
    model.addAttribute("employeeList", employeeList);
    return "employee_list";
}
```
##### b>创建employee\_list.html

```java
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Employee Info</title>
    <script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
</head>
<body>

    <table border="1" cellpadding="0" cellspacing="0" style="text-align: center;" id="dataTable">
        <tr>
            <th colspan="5">Employee Info</th>
        </tr>
        <tr>
            <th>id</th>
            <th>lastName</th>
            <th>email</th>
            <th>gender</th>
            <th>options(<a th:href="@{/toAdd}">add</a>)</th>
        </tr>
        <tr th:each="employee : ${employeeList}">
            <td th:text="${employee.id}"></td>
            <td th:text="${employee.lastName}"></td>
            <td th:text="${employee.email}"></td>
            <td th:text="${employee.gender}"></td>
            <td>
            <!-- 请求参数使用EL表达式的时候 需要使用➕拼接 -->
                <a class="deleteA" @click="deleteEmployee" th:href="@{'/employee/'+${employee.id}}">delete</a>
                <a th:href="@{'/employee/'+${employee.id}}">update</a>
            </td>
        </tr>
    </table>
</body>
</html>
```
## 删除

##### a>创建处理delete请求方式的表单

```java
<!-- 作用：通过超链接控制表单的提交，将post请求转换为delete请求 -->
<form id="delete_form" method="post">
    <!-- HiddenHttpMethodFilter要求：必须传输_method请求参数，并且值为最终的请求方式 -->
    <input type="hidden" name="_method" value="delete"/>
</form>
```
##### b>删除超链接绑定点击事件

引入vue.js

```java
<script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
```
删除超链接

```java
<a class="deleteA" @click="deleteEmployee" th:href="@{'/employee/'+${employee.id}}">delete</a>
```
通过vue处理点击事件

```java
<script type="text/javascript">
    var vue = new Vue({
        el:"#dataTable",
        methods:{
            //event表示当前事件
            deleteEmployee:function (event) {
                //通过id获取表单标签
                var delete_form = document.getElementById("delete_form");
                //将触发事件的超链接的href属性为表单的action属性赋值
                delete_form.action = event.target.href;
                //提交表单
                delete_form.submit();
                //阻止超链接的默认跳转行为
                event.preventDefault();
            }
        }
    });
</script>
```
##### c>控制器方法

```java
@RequestMapping(value = "/employee/{id}", method = RequestMethod.DELETE)
public String deleteEmployee(@PathVariable("id") Integer id){
    employeeDao.delete(id);
    // 删除后请求已经结束，应该改变地址所以重定向
    return "redirect:/employee";
}
```
##### c>开放静态资源访问

如果不开放无法访问到导入的vue.js

```java
<!-- 开放静态资源的访问 如果不加，则都是DispatcherServlert处理-->
<mvc:default-servlet-handler/>

<!-- 开启mvc注解驱动 如果不加，那所有请求都将被default servlet处理-->
<mvc:annotation-driver />
```
先由DispatcherServlet处理，如果处理不了就由default servlet处理

