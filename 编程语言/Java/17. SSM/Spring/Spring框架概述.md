
# Spring 是什么

1. Spring是一个轻量级的开源JavaEE框架
2. 解决企业应用开发的复杂性
3. Spring有两个核心部分：IOC和Aop
1. IOC：控制反转，把创建对象过程交给Spring管理
2. Aop：面向切片，不修改源代码进行功能增强
4. Spring特点
1. 非侵入式
2. 方便解耦，简化开发
3. Aop编程支持
4. 方便程序测试
5. 可以方便和其它框架进行整合
6. 方便进行事务操作
7. 降低API开发难度
8. 源码是经典学习规范
5. 广义Spring 为以Spring Framwork 为核心的Spring技术栈，包括Spring MVC SpringBoot Spring Cloud Spring Data Spring Security
6. 现在课程中，选取Spring6 要求Java17

# 入门案例

## 1.引入依赖

<https://repo.spring.io/ui/native/release/org/springframework/spring/>

![](Spring框架概述_001.png)

```java
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>6.0.2</version>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.9.1</version>
        </dependency>
    </dependencies>
```
## 2. 创建类

```java
package com.hqw.spring;

public class User {
    public void add() {
        System.out.println("add...");
    }
}
```
## 3. 创建Spring配置文件，通过配置文件创建对象

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 配置User对象创建 -->
    <bean id="user" class="com.hqw.spring.User"></bean>
</beans>
```
## 4. 测试代码

```java
public class TestSpring {
    @Test
    public void testAdd() {
        //1. 加载spring配置文件 classPath是src下
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        //2. 获取配置创建的对象
        User user = context.getBean("user", User.class);
        user.add();
    }
}
```
# 如何创建对象

1. 加载bean.xml配置文件
2. 对xml文件进行解析操作
3. 获取xml文件bean标签属性值
4. 使用反射根据类全路径创建对象

```java
Class<?> aClass = Class.forName("com.hqw.spring6.User");
User user = (User) Class.getDeclaredConstructor().newInstance();
```
5. bean对象最终存储在spring容器中，在spring源码底层就是一个map集合 `private final Map<String, BeanDefinition> beanDefinitionMap`key为唯一标识，value为类的定义b
