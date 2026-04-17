
# IOC底层原理

## 1. 什么是IOC

**控制反转**（Inversion of Control，缩写为**IoC**），是[面向对象编程](https://baike.baidu.com/item/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%BC%96%E7%A8%8B/254878)中的一种设计原则，可以用来减低计算机[代码](https://baike.baidu.com/item/%E4%BB%A3%E7%A0%81/86048)之间的[耦合度](https://baike.baidu.com/item/%E8%80%A6%E5%90%88%E5%BA%A6/2603938)。其中最常见的方式叫做[依赖注入](https://baike.baidu.com/item/%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5/5177233)（Dependency Injection，简称**DI**），还有一种方式叫“依赖查找”（Dependency Lookup）。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。

1. 控制反转，把对象创建和对象调用交给Spring进行管理，把Ioc管理的Java对象叫做Spring Bean。与使用关键字new创建的对象没有区别
2. IOC目的：降低耦合度

## 2. IOC底层原理

1. **xml解析、工厂模式、反射**
2. IOC过程
1. xml配置文件，创建对象的信息

`<bean id="dao" class="com.hqw.spring.UserDao"></bean>`

2. 通过抽象接口`BeanDefinitionReader`加载进IoC容器
3. IoC容器中通过BeanFactory工厂+反射进行实例化、初始化
4. `context.getBean("user")`得到对象
3. 目的：实现解耦合

## 3. 依赖注入（DI）

spring创建对象的过程中，将对象依赖属性通过配置进行注入

## 4. IOC接口（BeanFactory）

1. IOC思想基于IOC容器完成，IOC容器的底层就是对象工厂
2. Spring提供IOC容器实现的两种方式（两个接口）
1. **BeanFactory**：IOC容器基本实现，是Spring内部使用的接口，不提供给开发使用

加载配置文件不会创建对象，在使用的时候才会创建对象

2. **ApplicationContext**：BeanFactory的子接口，提供了更强大的功能，面向开发使用

加载配置文件的时候就会创建对象

![](IOC容器_001.png)

* ClassPathXmlApplicationContext -> src目录下,xml格式配置文件创建IOC
* FileSystemXmlApplicationContext -> 完整路径，xml格式配置文件创建IOC
* AnnotationConfigApplicationContext: 读取Java配置类创建IOC
* WebApplicationContext: web环境创建IOC，并将对象存入ServletContext域中
3. Spring提供多种方式配置
1. XML配置方式
2. 注解配置方式，从2.5版本开始提供
3. Java配置类方式，从3.0开始提供，通过Java类定义Bean, Bean之间的依赖关系和配置信息。通过`@Configuration``@Bean`等注解来实现

