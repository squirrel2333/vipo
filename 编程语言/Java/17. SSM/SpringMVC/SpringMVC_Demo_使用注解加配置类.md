
# 依赖

```java
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>6.0.6</version>
        </dependency>

        <!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>

        <!-- ServletAPI -->
        <dependency>
            <groupId>jakarta.platform</groupId>
            <artifactId>jakarta.jakartaee-web-api</artifactId>
            <version>9.1.0</version>
            <scope>provided</scope>
        </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.15.0</version>
    </dependency>
```
# 配置类

```java
//给handlerAdapter配置了json解析器
// EnableWebMvc注解还实现了添加 handlermapping，handleradaptor，可以不用再使用@Bean注解如下添加
@EnableWebMvc
@Configuration
@ComponentScan(basePackages = "com.hqw")
public class SpringMvcConfig implements WebMvcConfigurer {

    @Bean
    public HandlerMapping handlerMapping() {
        return new RequestMappingHandlerMapping();
    }

    @Bean
    public HandlerAdapter handlerAdapter() {
        return new RequestMappingHandlerAdapter();
    }
}
```
# Web初始化类

```java
/**
 * 1. SpringMVC提供的接口,是替代web.xml的方案,更方便实现完全注解方式ssm处理!
 * 2. AbstractAnnotationConfigDispatcherServletInitializer继承了WebApplicationInitializer，该类会在web初始化的时候执行onstart方法
 * 3. 在继承的类中创建了AnnotationConfigWebApplicationContext()ioc容器
 * 4. 在继承的类中调用了我们重写的方法getRootConfigClasses / getServletConfigClasses 自动加载我们提供的配置类，来初始化Servlet
 * 5. getServletMappings 返回的地址 设置DispatherServlet对应处理的地址
 */
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    /**
     * 指定service / mapper层的配置类
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return null;
    }

    /**
     * 指定springmvc的配置类
     * @return
     */
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { SpringMvcConfig.class };
    }

    /**
     * 设置dispatcherServlet的处理路径!
     * 一般情况下为 / 代表处理所有请求!
     */
    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}
```
# 请求路径/参数

```java
@Controller
public class HelloController {

    /**
     * @WebServlet("必须使用/开头")
     * @RequestMapping("不必使用/开头")
     *      1. 精准地址，也可以使用多个{"地址1","地址2"}
     *      2. 模糊匹配 *任意一层 **任意多层
     *      3. 类上加，匹配类上的地址+方法上的地址
     *      4. 指定method请求类型 method = RequestMethod.GET. 也可以用@GetMapping注解代替
     */
    @RequestMapping({"springmvc/hello","sss"}) //就是将我们的handler注册进handlerMapping
    @ResponseBody
    public String hello() {
        System.out.println("HelloController.hello");
        return "hello";
    }

    /**
     * 形参参数名=请求参数名即可匹配使用
     * /param/data1?name=hqw&age=12
     */
    @RequestMapping("param/data1")
    @ResponseBody
    public String data1(String name, String age) {
        System.out.println("name="+name+","+"age="+age);
        return name+":"+age;
    }

    /**
     * http://localhost:8080/param/data2?account=hqw&age=12
     * 带了RequestParam默认必须传递参数，并且value可以自定义请求参数的名称
     */
    @RequestMapping("param/data2")
    @ResponseBody
    public String data2(@RequestParam(value = "account") String name,
                        @RequestParam(required = false, defaultValue = "1") int age) {

        return name+":"+age;
    }

    /**
     * 使用集合接收值
     * http://localhost:8080/param/data3?params=1&params=2
     */
    @RequestMapping("param/data3")
    @ResponseBody
    public String data3(@RequestParam List<String> params) {
        params.forEach(System.out::print);
        return params.stream().collect(Collectors.joining(","));
    }

    /**
     * 使用实体类接收
     * http://localhost:8080/param/data4?name=hqw&age=8
     * 不能加@RequestParam
     */
    @RequestMapping("param/data4")
    @ResponseBody
    public String data4(User user) {
        return user.name+user.age;
    }

    /**
     * 动态路径使用@PathVariable注解
     * http://localhost:8080/path/hqw/123
     */
    @RequestMapping("path/{account}/{password}")
    @ResponseBody
    public String data5(@PathVariable(value = "account") String userName, @PathVariable String password) {
        return userName+":"+password;
    }

    /**
     * 使用post在request body代入json，json内容为User对象的属性
     * 浏览器返回 415不支持数据类型，Java原生不支持
     * 需要1. 导入Json处理依赖 2. handlerAdapter配置json转换器(在配置类加上@EnableWebMvc)
     * http://localhost:8080/json/data6
     */
    @PostMapping(("json/data6"))
    @ResponseBody
    public String data6(@RequestBody User user) {
        System.out.println(user.name+user.age);
        return "success";
    }
}
```
# Cookie

```java
@Controller
@RequestMapping("cookie")
public class CookieController {

    @RequestMapping("cookie")
    @ResponseBody
    public String data(@CookieValue(value = "cookieName") String value) {
        System.out.println();
        return value;
    }

    @GetMapping("/save")
    @ResponseBody
    public String save(HttpServletResponse response) {
        Cookie cookieName = new Cookie("cookieName", "xxxxx");
        response.addCookie(cookieName);
        return "ok";
    }
}
```
# 请求header

```java
@Controller
@RequestMapping("header")
public class HeaderController {

    @RequestMapping("header")
    @ResponseBody
    public String data1(@RequestHeader(value = "Host")String host) {
        return host;
    }
}
```
# 共享域数据

[域对象共享数据](https://www.yuque.com/squirrelv/hngok9/kwl3cnpi7b57ahzz "域对象共享数据")

# 前后端分离

![](SpringMVC_Demo_使用注解加配置类_001.png)

[SpringMVC的视图](https://www.yuque.com/squirrelv/hngok9/tf2tekm1iadc6o6g "SpringMVC的视图")

# 转发重定向

如果是项目下的资源，转发和重定向都一样都是项目下路径！都不需要添加项目根路径！

```java
@RequestMapping("/redirect-demo")
public String redirectDemo() {
    // 重定向到 /demo 路径
    return "redirect:/demo";
}

// 外部redirect
@RequestMapping("/redirect-demo")
public String redirectBaiduDemo() {
    // 重定向到 /demo 路径
    return "redirect:https://www.baidu.com";
}

@RequestMapping("/forward-demo")
public String forwardDemo() {
    // 转发到 /demo 路径
    return "forward:/demo";
}

```
# 返回Json

可以在方法上使用 @ResponseBody注解，用于将方法返回的对象序列化为 JSON 或 XML 格式的数据，并发送给客户端。在前后端分离的项目中使用！

具体来说，@ResponseBody 注解可以用来标识方法或者方法返回值，表示方法的返回值是要直接返回给客户端的数据，而不是由视图解析器来解析并渲染生成响应体（viewResolver没用）。

**如果类中每个方法上都标记了 @ResponseBody 注解，那么这些注解就可以提取到类上。**

```java
@Controller
@RequestMapping("backend")
public class BackendController {

    @RequestMapping(value = "data1", method = RequestMethod.POST)
    @ResponseBody
    public User data1(@RequestBody User user) {
        System.out.println("name:"+user.getName()+" age"+user.getAge());
        user.setName("hqw");
        user.setAge(18);
        return user;// user -> handlerAdapter -> json
    }

    // java:对象/map -> json:{}
    // java:list -> json:[]
    @RequestMapping(value = "data2")
    @ResponseBody
    public List<User> data2() {
        User user = new User();
        user.setName("ggg");
        user.setAge(12);
        ArrayList<User> objects = new ArrayList<>();
        objects.add(user);
        return objects;
    }
}
```
![](SpringMVC_Demo_使用注解加配置类_002.png)![](SpringMVC_Demo_使用注解加配置类_003.png)

类上的 @ResponseBody 注解可以和 @Controller 注解合并为 @RestController 注解。所以使用了 @RestController 注解就相当于给类中的每个方法都加了 @ResponseBody 注解。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {

  /**
   * The value may indicate a suggestion for a logical component name,
   * to be turned into a Spring bean in case of an autodetected component.
   * @return the suggested component name, if any (or empty String otherwise)
   * @since 4.0.1
   */
  @AliasFor(annotation = Controller.class)
  String value() default "";

}
```
# 静态资源

* DispatcherServlet 的 url-pattern 配置的是“/”
* url-pattern 配置“/”表示整个 Web 应用范围内所有请求都由 SpringMVC 来处理
* 对 SpringMVC 来说，必须有对应的 [@RequestMapping](/RequestMapping )  才能找到处理请求的方法
* 现在 images/mi.jpg 请求没有对应的 [@RequestMapping](/RequestMapping )  所以返回 404

```java
@EnableWebMvc
@Configuration
@ComponentScan(basePackages = "com.hqw")
public class SpringMvcConfig implements WebMvcConfigurer {

//    @Bean
//    public HandlerMapping handlerMapping() {
//        return new RequestMappingHandlerMapping();
//    }
//
//    @Bean
//    public HandlerAdapter handlerAdapter() {
//        return new RequestMappingHandlerAdapter();
//    }

    //开启静态资源处理 <mvc:default-servlet-handler/>
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
}
```
# 异常处理

发生异常 -》ControllerAdvice -》@ExceptionHandler(指定的异常) -> handler

```java
@ControllerAdvice
//@RestController = @ControllerAdvice + @ResponseBody
public class GlobalExceptionHandler {

    @ExceptionHandler(ArithmeticException.class)
    @ResponseBody
    public Object arithmeticExceptionHandler(ArithmeticException arithmeticException) {
        String message = arithmeticException.getMessage();
        System.out.println("message" + message);
        return message;
    }
}
```

```java
@RequestMapping("exception")
@RestController
public class ExecptionController {

    @RequestMapping("data1")

    public String data1() {
        int a = 1/ 0;
        return "success";
    }
}
```
# 拦截器

![](SpringMVC_Demo_使用注解加配置类_004.png)拦截器 Springmvc VS 过滤器 javaWeb：

* 相似点
+ 拦截：必须先把请求拦住，才能执行后续操作
+ 过滤：拦截器或过滤器存在的意义就是对请求进行统一处理
+ 放行：对请求执行了必要操作后，放请求过去，让它访问原本想要访问的资源
* 不同点
+ 工作平台不同
- 过滤器工作在 Servlet 容器中
- 拦截器工作在 SpringMVC 的基础上
+ 拦截的范围
- 过滤器：能够拦截到的最大范围是整个 Web 应用
- 拦截器：能够拦截到的最大范围是整个 SpringMVC 负责的请求
+ IOC 容器支持
- 过滤器：想得到 IOC 容器需要调用专门的工具方法，是间接的
- 拦截器：它自己就在 IOC 容器中，所以可以直接从 IOC 容器中装配组件，也就是可以直接得到 IOC 容器的支持

```java
public class Process01Interceptor implements HandlerInterceptor {

    // if( ! preHandler()){return;}
    // 在处理请求的目标 handler 方法前执行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler);
        System.out.println("Process01Interceptor.preHandle");

        // 返回true：放行
        // 返回false：不放行
        return true;
    }

    // 在目标 handler 方法之后，handler报错不执行!
    // 对结果进行处理
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler + ", modelAndView = " + modelAndView);
        System.out.println("Process01Interceptor.postHandle");
    }

    // 渲染视图之后执行(最后),一定执行!
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler + ", ex = " + ex);
        System.out.println("Process01Interceptor.afterCompletion");
    }
}
```

```java
@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器
@Configuration
@ComponentScan(basePackages = {"com.atguigu.controller","com.atguigu.exceptionhandler"}) //TODO: 进行controller扫描
//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
public class SpringMvcConfig implements WebMvcConfigurer {

    // //配置jsp对应的视图解析器
    // @Override
    // public void configureViewResolvers(ViewResolverRegistry registry) {
    //     //快速配置jsp模板语言对应的
    //     registry.jsp("/WEB-INF/views/",".jsp");
    // }

    // //开启静态资源处理 <mvc:default-servlet-handler/>
    // @Override
    // public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
    //     configurer.enable();
    // }

    //添加拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
        registry.addInterceptor(new Process01Interceptor());

        //精准匹配,设置拦截器处理指定请求 路径可以设置一个或者多个,为项目下路径即可
        //addPathPatterns("/common/request/one") 添加拦截路径
        //也支持 /* 和 /** 模糊路径。 * 任意一层字符串 ** 任意层 任意字符串
        registry.addInterceptor(new Process01Interceptor()).addPathPatterns("/common/request/one","/common/request/tow");

        //排除匹配,排除应该在匹配的范围内排除
        //addPathPatterns("/common/request/one") 添加拦截路径
        //excludePathPatterns("/common/request/tow"); 排除路径,排除应该在拦截的范围内
        registry.addInterceptor(new Process01Interceptor())
                .addPathPatterns("/common/request/one","/common/request/tow")
                .excludePathPatterns("/common/request/tow");
    }

}
```
配置多个拦截器的执行顺序

1. preHandle() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置顺序调用各个 preHandle() 方法。
2. postHandle() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置相反的顺序调用各个 postHandle() 方法。
3. afterCompletion() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置相反的顺序调用各个 afterCompletion() 方法。

# 参数校验

在 Web 应用三层架构体系中，表述层负责接收浏览器提交的数据，业务逻辑层负责数据的处理。为了能够让业务逻辑层基于正确的数据进行处理，我们需要在表述层对数据进行检查，将错误的数据隔绝在业务逻辑层之外。

1. **校验概述**
JSR 303 是 Java 为 Bean 数据合法性校验提供的标准框架，它已经包含在 JavaEE 6.0 标准中。JSR 303 通过在 Bean 属性上标注类似于 @NotNull、[@Max](/Max )  等标准的注解指定校验规则，并通过标准的验证接口对Bean进行验证。

|  |  |
| --- | --- |
| 注解 | 规则 |
| [@Null](/Null )  | 标注值必须为 null |
| [@NotNull](/NotNull )  | 标注值不可为 null |
| [@AssertTrue](/AssertTrue )  | 标注值必须为 true |
| [@AssertFalse](/AssertFalse )  | 标注值必须为 false |
| [@Min(value)](/value)  | 标注值必须大于或等于 value |
| [@Max(value)](/value)  | 标注值必须小于或等于 value |
| [@DecimalMin(value)](/value)  | 标注值必须大于或等于 value |
| [@DecimalMax(value)](/value)  | 标注值必须小于或等于 value |
| @Size(max,min) | 标注值大小必须在 max 和 min 限定的范围内 |
| @Digits(integer,fratction) | 标注值值必须是一个数字，且必须在可接受的范围内 |
| [@Past](/Past )  | 标注值只能用于日期型，且必须是过去的日期 |
| [@Future](/Future )  | 标注值只能用于日期型，且必须是将来的日期 |
| [@Pattern(value)](/value)  | 标注值必须符合指定的正则表达式 |

JSR 303 只是一套标准，需要提供其实现才可以使用。Hibernate Validator 是 JSR 303 的一个参考实现，除支持所有标准的校验注解外，它还支持以下的扩展注解：

|  |  |
| --- | --- |
| 注解 | 规则 |
| [@Email](/Email )  | 标注值必须是格式正确的 Email 地址 |
| [@Length](/Length )  | 标注值字符串大小必须在指定的范围内 |
| [@NotEmpty](/NotEmpty )  | 标注值字符串不能是空字符串 |
| [@Range](/Range )  | 标注值必须在指定的范围内 |

Spring 4.0 版本已经拥有自己独立的数据校验框架，同时支持 JSR 303 标准的校验框架。Spring 在进行数据绑定时，可同时调用校验框架完成数据校验工作。在SpringMVC 中，可直接通过注解驱动 @EnableWebMvc 的方式进行数据校验。Spring 的 LocalValidatorFactoryBean 既实现了 Spring 的 Validator 接口，也实现了 JSR 303 的 Validator 接口。只要在Spring容器中定义了一个LocalValidatorFactoryBean，即可将其注入到需要数据校验的 Bean中。Spring本身并没有提供JSR 303的实现，所以必须将JSR 303的实现者的jar包放到类路径下。

配置 @EnableWebMvc后，SpringMVC 会默认装配好一个 LocalValidatorFactoryBean，通过在处理方法的入参上标注 @Validated 注解即可让 SpringMVC 在完成数据绑定后执行数据校验的工作。

```java
<!-- 校验注解实现-->
<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>8.0.0.Final</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator-annotation-processor -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator-annotation-processor</artifactId>
    <version>8.0.0.Final</version>
</dependency>
```

```java
/**
 * projectName: com.atguigu.pojo
 */
public class User {
    //age   1 <=  age < = 150
    @Min(10)
    private int age;

    //name 3 <= name.length <= 6
    @Length(min = 3,max = 10)
    private String name;

    //email 邮箱格式
    @Email
    private String email;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

```java
@RestController
@RequestMapping("user")
public class UserController {

    /**
     * @Validated 代表应用校验注解! 必须添加!
     * 不加BindingResult会给前端返回错误异常，加入之后捕获错误，自定义返回结果
     */
    @PostMapping("save")
    public Object save(@Validated @RequestBody User user,
                       //在实体类参数和 BindingResult 之间不能有任何其他参数, BindingResult可以接受错误信息,避免信息抛出!
                       BindingResult result){
       //判断是否有信息绑定错误! 有可以自行处理!
        if (result.hasErrors()){
            System.out.println("错误");
            String errorMsg = result.getFieldError().toString();
            return errorMsg;
        }
        //没有,正常处理业务即可
        System.out.println("正常");
        return user;
    }
}
```
