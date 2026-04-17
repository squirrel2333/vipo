
```java
hqw.url=jdbc:mysql://1.94.55.17:3306/studb
hqw.driver=com.mysql.cj.jdbc.Driver
hqw.username=root
hqw.password=hqw3.1415926
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${hqw.url}"/>
        <property name="username" value="${hqw.username}"/>
        <property name="password" value="${hqw.password}"/>
        <property name="driverClassName" value="${hqw.driver}"/>

    </bean>

    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="druidDataSource"/>
    </bean>
</beans>
```

```java
package com.hqw.demo;

public class Student {

    private Integer id;
    private String name;
    private Integer age;
    private String classes;

    public Student() {
    }

    public Student(Integer id, String name, Integer age, String classes) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.classes = classes;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getClassNo() {
        return classes;
    }

    public void setClasses(String classes) {
        this.classes = classes;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", classes='" + classes + '\'' +
                '}';
    }
}

```

```java
mysql> show create table students\G
*************************** 1. row ***************************
       Table: students
Create Table: CREATE TABLE `students` (
  `id` int(11) NOT NULL,
  `name` varchar(10) NOT NULL,
  `age` int(11) DEFAULT NULL,
  `class` varchar(50) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1
1 row in set (0.00 sec)
```

```java
public class JdbcTemplateTest {

    ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-demo.xml");
    @Test
    public void testDML() {

        JdbcTemplate jdbcTemplate = applicationContext.getBean(JdbcTemplate.class);

        String insert = "INSERT INTO students VALUES (?,?,?,?);";
        int row = jdbcTemplate.update(insert, 4, "hqw", 18, "class3");
        System.out.println(row);
    }

    @Test
    public void testDQL() {
        JdbcTemplate jdbcTemplate = applicationContext.getBean(JdbcTemplate.class);
        String query = "SELECT id, name, age, class as classes FROM students where id = ?;";
        Student student = jdbcTemplate.queryForObject(query, (rs, rowNum) -> {
            // 自己处理结果映射
            Student student1 = new Student();
            student1.setId(rs.getInt("id"));
            student1.setName(rs.getString("name"));
            student1.setAge(rs.getInt("age"));
            student1.setClasses(rs.getString("classes"));
            return student1;
        },2);

        System.out.println(student);
    }

    @Test
    public void testDQLForListPojo() {
        String query = "select id, name, age, class AS classes from students;";
        JdbcTemplate jdbcTemplate = applicationContext.getBean(JdbcTemplate.class);
        /*
        query可以返回集合!
        BeanPropertyRowMapper就是封装好RowMapper的实现,要求属性名和列名相同即可
         */
        List<Student> student = jdbcTemplate.query(query, new BeanPropertyRowMapper<>(Student.class));
        System.out.println(student);
    }
```
