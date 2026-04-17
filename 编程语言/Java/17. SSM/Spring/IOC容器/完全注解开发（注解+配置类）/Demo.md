
```java
package com.hqw.fullannotation;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.beans.factory.config.ConfigurableBeanFactory;
import org.springframework.context.annotation.*;
import org.springframework.jdbc.core.JdbcTemplate;

@Configuration
@PropertySource("classpath:jdbc.properties")
@ComponentScan(basePackages = {"com.hqw.fullannotation"})
public class MyConfiguration {
    @Value("${hqw.url}")
    private String url;
    @Value("${hqw.username}")
    private String username;
    @Value("${hqw.password}")
    private String password;
    @Value("${hqw.driver}")
    private String driver;

    @Bean(destroyMethod = "close")
    public DruidDataSource druidDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);
        druidDataSource.setDriverClassName(driver);
        return druidDataSource;
    }
    @Bean
    @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
    public JdbcTemplate createJdbcTemplate(DruidDataSource druidDataSource) {
        return new JdbcTemplate(druidDataSource);
    }
}

```

```java
package com.hqw.fullannotation;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public class StudentDaoImpl implements StudentDao{

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public Student queryStudentById(Integer id) {
        String sql = "SELECT id, name, age, class AS classes FROM students where id = ?;";
        return jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(Student.class),id);
    }

    @Override
    public List<Student> queryAllStudent() {
        String sql = "SELECT id, name, age, class AS classes FROM students";
        return jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(Student.class));
    }

    @Override
    public Integer insertStudentInfo(int id, String name, int age, String classes) {
        String sql = "INSERT INTO students VALUES (?,?,?,?);";
        return jdbcTemplate.update(sql, id, name, age, classes);
    }
}

```

```java
package com.hqw.fullannotation;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    public List<Student> getAllStudents() {
        System.out.println("Execute Multi-Search Service");
        return studentDao.queryAllStudent();
    }

    public Student getStudent(Integer id) {
        System.out.println("Execute Single-Search Service");
        return studentDao.queryStudentById(id);
    }

    public int insertData(int id, String name, int age, String classes) {
        System.out.println("Execute Insert Service");
        return studentDao.insertStudentInfo(id, name, age, classes);
    }
}

```

```java
package com.hqw.fullannotation;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

import java.util.List;

@Controller
public class StudentController {

    @Autowired
    private StudentService studentService;

    public void execute() {
        List<Student> allStudents = studentService.getAllStudents();
        System.out.println("Query Successfully");
        allStudents.forEach(System.out::println);

        int i = studentService.insertData(10, "wsm", 19, "class4");
        if (i == 1) {
            System.out.println("Insert Successfully");
            Student student = studentService.getStudent(10);
            System.out.println("Student data is:");
            System.out.println(student);
        }

    }
}

```

```java
package com.hqw.fullannotation;

import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Test1 {

    @Test
    public void test1() {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MyConfiguration.class);
        StudentController bean = applicationContext.getBean(StudentController.class);
        bean.execute();
    }
}

```
