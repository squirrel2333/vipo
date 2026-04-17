
## 一、SSM整合理解

### 1.1 什么是SSM整合？

**微观**：将学习的Spring SpringMVC Mybatis框架应用到项目中!

* SpringMVC框架负责控制层
* Spring 框架负责整体和业务层的声明式事务管理
* MyBatis框架负责数据库访问层

**宏观**：Spring接管一切（将框架核心组件交给Spring进行IoC管理），代码更加简洁。

* SpringMVC管理表述层、SpringMVC相关组件
* Spring管理业务层、持久层、以及数据库相关（DataSource,MyBatis）的组件
* 使用IoC的方式管理一切所需组件

**实施**：通过编写配置文件，实现SpringIoC容器接管一切组件。

### 1.2 SSM整合核心问题明确

#### 1.2.1 第一问：SSM整合需要几个IoC容器？

两个容器

本质上说，整合就是将三层架构和框架核心API组件交给SpringIoC容器管理！

一个容器可能就够了，但是我们常见的操作是创建两个IoC容器（web容器和root容器），组件分类管理！

这种做法有以下好处和目的：

1. 分离关注点：通过初始化两个容器，可以将各个层次的关注点进行分离。这种分离使得各个层次的组件能够更好地聚焦于各自的责任和功能。
2. 解耦合：各个层次组件分离装配不同的IoC容器，这样可以进行解耦。这种解耦合使得各个模块可以独立操作和测试，提高了代码的可维护性和可测试性。
3. 灵活配置：通过使用两个容器，可以为每个容器提供各自的配置，以满足不同层次和组件的特定需求。每个配置文件也更加清晰和灵活。

总的来说，初始化两个容器在SSM整合中可以实现关注点分离、解耦合、灵活配置等好处。它们各自负责不同的层次和功能，并通过合适的集成方式协同工作，提供一个高效、可维护和可扩展的应用程序架构！

#### 1.2.2 第二问：每个IoC容器对应哪些类型组件？

![](SSM整合_001.png)

总结：

|  |  |
| --- | --- |
| 容器名 | 盛放组件 |
| web容器 | web相关组件（controller,springmvc核心组件） |
| root容器 | 业务和持久层相关组件（service,aop,tx,dataSource,mybatis,mapper等） |

#### 1.2.3 第三问：IoC容器之间关系和调用方向？

情况1：两个无关联IoC容器之间的组件无法注入！

![](SSM整合_002.png)

情况2：子IoC容器可以单向的注入父IoC容器的组件！

![](SSM整合_003.png)

![](./attachments/SSM整合_004)结论：web容器是root容器的子容器，父子容器关系。

* 父容器：root容器，盛放service、mapper、mybatis等相关组件
* 子容器：web容器，盛放controller、web相关组件

源码体现：

FrameworkServlet  655行！

```java
protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
    Class<?> contextClass = getContextClass();
    if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
      throw new ApplicationContextException(
          "Fatal initialization error in servlet with name '" + getServletName() +
          "': custom WebApplicationContext class [" + contextClass.getName() +
          "] is not of type ConfigurableWebApplicationContext");
    }
    ConfigurableWebApplicationContext wac =
        (ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);

    wac.setEnvironment(getEnvironment());
    //wac 就是web ioc容器
    //parent 就是root ioc容器
    //web容器设置root容器为父容器，所以web容器可以引用root容器
    wac.setParent(parent);
    String configLocation = getContextConfigLocation();
    if (configLocation != null) {
      wac.setConfigLocation(configLocation);
    }
    configureAndRefreshWebApplicationContext(wac);

    return wac;
  }
```
![](SSM整合_005.png)

#### 1.2.4 第四问：具体多少配置类以及对应容器关系？

配置类的数量不是固定的，但是至少要两个，为了方便编写，我们可以三层架构每层对应一个配置类，分别指定两个容器加载即可！

![](SSM整合_006.png)

建议配置文件：

|  |  |  |
| --- | --- | --- |
| 配置名 | 对应内容 | 对应容器 |
| WebJavaConfig | controller,springmvc相关 | web容器 |
| ServiceJavaConfig | service,aop,tx相关 | root容器 |
| MapperJavaConfig | mapper,datasource,mybatis相关 | root容器 |

#### 1.2.5 第五问：IoC初始化方式和配置位置？

在web项目下，我们可以选择web.xml和配置类方式进行ioc配置，推荐配置类。

对于使用基于 web 的 Spring 配置的应用程序，建议这样做，如以下示例所示：

```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

  //指定root容器对应的配置类
  //root容器的配置类
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class<?>[] { ServiceJavaConfig.class,MapperJavaConfig.class };
  }

  //指定web容器对应的配置类 webioc容器的配置类
  @Override
  protected Class<?>[] getServletConfigClasses() {
    return new Class<?>[] { WebJavaConfig.class };
  }

  //指定dispatcherServlet处理路径，通常为 /
  @Override
  protected String[] getServletMappings() {
    return new String[] { "/" };
  }
}
```
图解配置类和容器配置：

![](SSM整合_007.png)

