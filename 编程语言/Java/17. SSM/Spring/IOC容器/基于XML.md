## 什么是Bean管理

1. Spring创建对象
2. Spring注入属性

## 创建对象

1. 在Spring配置文件中使用bean标签，标签里添加属性，就可以实现对象的创建
2. 在bean标签中的属性
1. id属性：唯一标识
2. class属性：类的全路径（包路径）
3. 创建对象时，默认执行无参构造

```java
ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
// 通过id获取
User user1 = (User) context.getBean("user");

// 通过类型获取(也可以通过接口类型）
User user2 = context.getBean(User.class);

// 通过id和类型获取
User user3 = context.getBean("user", User.class);
```
* 如果有多个同样类型的Bean，通过类型获取会失败 （底层instanceof判断对象类型）
* 如果组件类实现了接口，在配置文件中配置了实现类的bean，那么根据接口的类型可以get到Bean
* **如果一个接口有多个实现类，都配置了bean，那么根据接口类型获取不到bean**

### 静态工厂类创建对象

```java
public class UserFactory {

    private static final User user = new User();
    private UserFactory() {}

    public static User createUser() {
        return user;
    }
}
```

```java
<bean id="user2" class="com.hqw.spring.UserFactory" factory-method="createUser"/>
```
### 非静态工厂创建对象

```java
public class UserFactory2 {

    private static User user = new User();
    public User createUser() {
        return user;
    }
}

```

```java
    <bean id="userFactory2" class="com.hqw.spring.UserFactory2"/>
    <bean id="user3" factory-bean="userFactory2" factory-method="createUser"/>
```
## 注入属性

DI: 依赖注入，就是注入属性

### 使用set方法

1. 创建类，定义set方法
2. 配置文件配置属性注入

```java
<bean id="book" class="com.hqw.spring.Book">
	<property name="bname" value="易筋经"></property>
</bean>
```
### 使用有参构造

1. 创建类，定义有参构造方法
2. 在配置文件中进行配置

```java
<bean id="orders" class="com.atguigu.spring5.Orders">
  <constructor-arg name="oname" value="avc"></constructor-arg>
  <constructor-arg name="address" value="China"></constructor-arg>
</bean>
```

```java
public void testOrders() {
  // 加载Spring配置
  ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
  // 获取配置创建的对象
  Orders orders = context.getBean("orders", Orders.class);
  //就可以调用Orders中的方法，得到根据配置文件修改后的属性值
  orders.orderTest();
}
```
### 注入特殊值属性

1. null值

```java
<property name="address">
    <null/>
</property>
```
2. xml实体

```java
<!-- 小于号在XML文档中用来定义标签的开始，不能随便使用 -->
<!-- 解决方案一：使用XML实体来代替 -->
<property name="expression" value="a &lt; b"/>
```
3. 属性值包含特殊符号放到CDATA区里

```java
<property name="address">
	<value><![CDATA[<<南京>>]]></value>
</property>
```
### 注入对象类型属性

#### 外部bean

1. 创建两个类：service类和dao类
2. 在service调用dao里面的方法

```java
public class UserService {
  private UserDao userDao;

  public void setUserDao(UserDao userDao) {
  	this.userDao = userDao;
  }
  public void add() {
    userDao.update();
  }
}
```
3. 在spring配置文件中进行配置

```java
<bean id="userService" class="com.atguigu.spring5.service">
  <!-- 注入userDao对象
		name属性：类里面的属性名称
		ref属性：创建userDao对象bean标签id值
	-->
  <property name="userDao" ref="userDaoImpl"></property>
</bean>
<bean id="userDaoImpl" class="com.atguigu.spring5.dao.UserDaoImpl"></bean>
```
4. 测试类中

```java
public class TestBean {
    @Test
    public void testAdd() {
        ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");
        // 获取配置创建的对象
        UserService userService = context.getBean("userService", UserService.class);
        userService.add();
    }
}
```
#### 内部bean

1. 一对多关系：部门和员工

```java
public class Dept {
	private String dname;
    public void setDname(String dname) {
    	this.dname = dname;
    }
}
```

```java
public class Emp {
    private String ename;
    private String gender;
    // 员工属于某一个部门
    private Dept dept;
    public void setEname(String ename) {
        this.ename= ename;
    }
    public void setGender(String gender) {
        this.gender = gender;
    }
}
```

```java
<bean id="" class="com.spring.Emp">
    <!--普通属性-->
    <property name="ename" value="lucy"></property>
    <property name="gender" value="girl"></property>
    <!--对象类型属性-->
    <property name="dept">
        <!--内部bean-->
        <bean id="dept" class="com.spring.Dept">
            <property name="dname" value="securty"></property>
        </bean>
    </property>
</bean>
```
#### 级联赋值

* 如果Emp类中有getDept方法

```java
<bean id="emp" class="com.spring.Emp">
    <!--普通属性-->
    <property name="ename" value="lucy"></property>
    <property name="gender" value="girl"></property>
    <property name="dept" ref="dept"></property>
    <!--级联赋值 name被覆盖为tech..-->
    <property name="dept.name" value="technique"></property>
</bean>

<bean id="dept" class="com.atguigu.spring.Dept">
  <property name="dname" value="finance"></property>
</bean>
```
### 注入数组类型属性

```java
<bean id="emp" class="...">
  <property name="ename" value="lucy"></property>
  <property name="age" value="20"></property>
  <property name="loves">
    <array>
      <value>chifan</value>
      <value>shuijiao</value>
    </array>
  </property>
</bean>
```
### 注入集合属性

#### 1. 注入数组类型属性

#### 2. 注入List集合类型属性

#### 3. 注入Map集合类型属性

```java
public class Stu {
  private String[] courses;
  private List<String> list;
  private Map<String, String> maps;
  private List<Course> list2;
  ...set get;
}
```

```java
<bean id="stu" class="com.spring.Stu">
    <!--数组类型属性注入-->
    <property name="course">
        <array>
            <value>java课程</value>
            <value>数据库</value>
        </array>
    </property>
    <!--list类型注入-->
    <property name="list">
        <list>
            <value>ww</value>
            <value>ll</value>
        </list>
    </property>
    <!--map类型-->
    <property name="maps">
        <map>
            <entry key="JAVA" value="java"></entry>
            <entry key="PHP" value="php"></entry>
        </map>
    </property>
  	<!-- 创建对象类型的集合 -->
  	<property name="list2">
        <list>
          <ref bean="course1"></ref>
          <ref bean="course2"></ref>
        </list>
  	</property>
</bean>

<!-- 创建多个对象 -->
<bean id="course1" class="com.spring.Course">
  <property name="cname" value="Spring框架"></property>
</bean>
<bean id="course2" class="com.spring.Course">
  <property name="cname" value="MyBatis框架"></property>
</bean>
```
#### 4. 引用集合类型的bean方法

1. 在spring配置文件中引入命名空间util

```java
<beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:util="http://www.springframework.org/schema/util"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
</beans>
```
2. 使用util标签完成list集合注入

```java
<!-- 提取 -->
<util:list id="bookList">
  <value>xxx</value>
  <value>yyy</value>
  <value>zzz</value>
</util:list>

<util:map id="map">
  <entry>
    <key>
      <value>100</value>
    </key>
    <ref bean="xxxx"></ref>
  </entry>
</util>

<!-- 使用 -->
<bean id="book" class="com.hqw.spring.Book">
  <property name="list" ref="bookList"></property>
</bean>
```
### P命名空间注入

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/util
       http://www.springframework.org/schema/util/spring-util.xsd
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="studentSix" class="com.atguigu.spring6.bean.Student"
    p:id="1006" p:name="小明" p:clazz-ref="clazzOne" p:teacherMap-ref="teacherMap">
  </bean>
```
### 引入外部属性文件

#### 1. 引入数据库相关依赖

```java
        <!-- MySQL驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.30</version>
        </dependency>

        <!-- 数据源 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.2.15</version>
        </dependency>
```
#### 2. 创建外部属性文件，properties格式，定义数据信息

```java
jdbc.user=root
jdbc.password=123456
jdbc.url=jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
jdbc.driver=com.mysql.cj.jdbc.Driver
```
#### 3. 创建Spring配置文件，引入context命名空间，引入属性文件，使用表达式注入

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

```

```java
    <context:property-placeholder location="classpath:jdbc.properties"/>
```

```java
DruidDataSource dataSource = new DruidDataSource();
dataSource.setUrl("jdbc:mysql://localhost:3306/spring?serverTimezone=UTC");
dataSource.setUsername("root");
dataSource.setPassword("root");
dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
```

```java
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.userName}"></property>
        <property name="password" value="${jdbc.password}"></property>
        <property name="driverClassName" value="${jdbc.driver}"></property>
    </bean>
```
## FactoryBean

FactoryBean是Spring提供的一种整合第三方框架的常用机制。和普通的bean不同，配置一个FactoryBean类型的bean，在获取bean的时候得到的并不是class属性中配置的这个类的对象，而是getObject()方法的返回值。通过这种机制，Spring可以帮我们把复杂组件创建的详细过程和繁琐细节都屏蔽起来，只把最简洁的使用界面展示给我们。

将来我们整合Mybatis时，Spring就是通过FactoryBean机制来帮我们创建SqlSessionFactory对象的。

1. Spring有两种bean，一种是普通bean，另外一种工程bean
1. 普通bean：在配置文件中定义的类型就是返回类型
2. 工厂bean：配置文件中bean的类型可以和返回类型不同

**FactoryBean** 是 Spring 中一种特殊的 bean，可以在 getObject() 工厂方法自定义的逻辑创建Bean！是一种能够生产其他 Bean 的 Bean。FactoryBean 在容器启动时被创建，而在实际使用时则是通过调用 getObject() 方法来得到其所生产的 Bean。因此，FactoryBean 可以自定义任何所需的初始化逻辑，生产出一些定制化的 bean。

一般情况下，整合第三方框架，都是通过定义FactoryBean实现！！！

**BeanFactory** 是 Spring 框架的基础，其作为一个顶级接口定义了容器的基本行为，例如管理 bean 的生命周期、配置文件的加载和解析、bean 的装配和依赖注入等。BeanFactory 接口提供了访问 bean 的方式，例如 getBean() 方法获取指定的 bean 实例。它可以从不同的来源（例如 Mysql 数据库、XML 文件、Java 配置类等）获取 bean 定义，并将其转换为 bean 实例。同时，BeanFactory 还包含很多子类（例如，ApplicationContext 接口）提供了额外的强大功能。

总的来说，FactoryBean 和 BeanFactory 的区别主要在于前者是用于创建 bean 的接口，它提供了更加灵活的初始化定制功能，而后者是用于管理 bean 的框架基础接口，提供了基本的容器功能和 bean 生命周期管理。

2. 步骤
1. 创建类。让这个类作为工厂bean，实现接口FactoryBean
2. 实现接口里的方法，在实现的方法中定义返回的bean类型

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="mybean" class="com.hqw.spring.MyBean"></bean>
</beans>
```

```java

public class MyBean implements FactoryBean<Book> {
    // 默认返回true，改为false创建时产生多例对象
    @Override
    public boolean isSingleton() {
        return FactoryBean.super.isSingleton();
    }

    // 定义返回bean
    @Override
    public Book getObject() throws Exception {
        Book book = new Book();
        //不能在xml中注入，因为配置文件是工厂bean
        book.setBname();
        book.getBname();
        return book;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }
}

```

```java
public class Test04 {
    @Test
    public void test() {
        ApplicationContext context = new ClassPathXmlApplicationContext("config5.xml");
        // 创建的是需要的对象bean
        Book book = context.getBean("mybean", Book.class);
        System.out.println(book);

        // 工厂bean也被创建 名字是&mybean
        Object bookFactory = context.getBean("&mybean");
    }
}
```
## bean作用域

1. Spring中能设置创建的bean实例是一个单实例还是多实例
2. 在spring配置文件bean标签里有scope用于设置多实例
3. scope属性值

|  |  |  |
| --- | --- | --- |
| 取值 | 含义 | 创建对象的时机 |
| singleton（默认） | 在IOC容器中，这个bean的对象始终为单实例 | IOC容器初始化时 |
| prototype | 这个bean在IOC容器中有多个实例 | 获取bean时 |

```java
<bean id="book" class="com.spring.hqw.Book" scope="prototype"></bean>
```

```java
public class Test04 {
    @Test
    public void test() {
        ApplicationContext context = new ClassPathXmlApplicationContext("config5.xml");
        Book book1 = context.getBean("book", Book.class);
        Book book2 = context.getBean("book", Book.class);
        System.out.println(book1);
        System.out.println(book2);
        // 输出的对象不同
    }
}
```
3. singleton和prototype区别

除了实例数量，**设置为singleton，加载spring配置文件时就会创建实例对象。设置为prototype，不是在加载spring配置文件时候创建对象，而是在getBean时创建**

4. scope还可以设置为 request 或者 session

表示创建对象时，将bean对象放到request或session对象中

## bean生命周期

* bean对象创建（调用无参构造器）
* 给bean对象设置属性 (set方法)
* bean的后置处理器（初始化之前）
* bean对象初始化（需在配置bean时指定初始化方法）
* bean的后置处理器（初始化之后）
* bean对象就绪可以使用
* bean对象销毁（需在配置bean时指定销毁方法）

```java
    <bean id="orders" class="com.hqw.spring.Orders" init-method="initMethod" destroy-method="destroyMethod">
        <property name="name" value="hi" ></property>
    </bean>
```

```java
public class Orders {
    public Orders() {
        System.out.println("第一步 执行无参构造");
    }

    private String oname;

    public void setName(String oname) {
        this.oname = oname;
        System.out.println("第二步，执行set方法");
    }

    public void initMethod() {
        System.out.println("第三步，执行初始化方法");
    }

    public void destroyMethod() {
        System.out.println("第五步 销毁方法");
    }
}
```

```java
public class Test04 {
    @Test
    public void test() {
        ApplicationContext context = new ClassPathXmlApplicationContext("config6.xml");
        Orders orders = context.getBean("orders", Orders.class);

        System.out.println("第四步 使用对象");
        // 手动调用销毁
        ((ClassPathXmlApplicationContext)context).close();
    }
}
```
### bean后置处理器

1. 通过构造器创建bean实例（无参构造）
2. 为bean的属性设置值和对其他bean引用（调用set方法）
3. 把bean实例传递bean前置处理的方法`postProcessBeforeInitialization()`
4. 调用bean的初始化方的方法（需要进行配置）
5. 把bean实例传递bean后置处理的方法`postProcessAfterInitialization()`
6. bean可以使用了（对象获取到了）
7. 当容器关闭时候，调用bean销毁的方法（需要配置销毁的方法）

#### 后置处理方法

1. 实现类，实现BeanPostProcessor接口
2. 配置后置处理器

```java
public class MyBeanPost implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("后置处理器 初始化前执行");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("后置处理器 初始化后执行");
        return bean;
    }
}
```

```java
<bean id="myBeanPost" class="com.hqw.spring.MyBeanPost"></bean>
```
## xml自动配置

根据指定装配规则（属性名称或类型），spring自动将匹配的属性值进行注入

```java
    <bean id="emp" class="com.hqw.spring.autowire.Emp" autowire="byName">
<!--        <property name="dept" ref="dept"></property>-->
    </bean>
    <bean id="dept" class="com.hqw.spring.autowire.Dept"></bean>
```
* `byName` 根据属性名称注入：注入值bean的id值和类属性名称一样
* `byType`根据属性类型注入：配置中只有一个该类型的bean
