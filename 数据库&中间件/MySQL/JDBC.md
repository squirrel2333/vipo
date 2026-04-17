JDBC

1、JDBC是什么？

 Java DataBase Connectivity（Java语言连接数据库）

2、JDBC的本质是什么？

 JDBC是SUN公司制定的一套接口（interface）

  java.sql.\*; (这个软件包下有很多接口。)

 接口都有调用者和实现者。

 面向接口调用、面向接口写实现类，这都属于面向接口编程。

 为什么要面向接口编程？

  解耦合：降低程序的耦合度，提高程序的扩展力。

  多态机制就是非常典型的：面向抽象编程。（不要面向具体编程）

   建议：

    Animal a = new Cat();

    Animal a = new Dog();

    // 喂养的方法

    public void feed(Animal a){ // 面向父类型编程。

    }

   不建议：

    Dog d = new Dog();

    Cat c = new Cat();

 思考：为什么SUN制定一套JDBC接口呢？

  因为每一个数据库的底层实现原理都不一样。

  Oracle数据库有自己的原理。

  MySQL数据库也有自己的原理。

  MS SqlServer数据库也有自己的原理。

  ....

  每一个数据库产品都有自己独特的实现原理。

 JDBC的本质到底是什么？

  一套接口。

3、JDBC开发前的准备工作，先从官网下载对应的驱动jar包，然后将其配置到环境变量classpath当中。

 classpath=.;D:\course\06-JDBC\resources\MySql Connector Java 5.1.23\mysql-connector-java-5.1.23-bin.jar

 以上的配置是针对于文本编辑器的方式开发，使用IDEA工具的时候，不需要配置以上的环境变量。

 IDEA有自己的配置方式。

4、JDBC编程六步（需要背会）

 第一步：注册驱动（作用：告诉Java程序，即将要连接的是哪个品牌的数据库）

 第二步：获取连接（表示JVM的进程和数据库进程之间的通道打开了，这属于进程之间的通信，重量级的，使用完之后一定要关闭通道。）

 第三步：获取数据库操作对象（专门执行sql语句的对象）

 第四步：执行SQL语句（DQL DML....）

 第五步：处理查询结果集（只有当第四步执行的是select语句的时候，才有这第五步处理查询结果集。）

 第六步：释放资源（使用完资源之后一定要关闭资源。Java和数据库属于进程间的通信，开启之后一定要关闭。）

```
import java.sql.*;

public class JDBCTest01 {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;

        try{
            //1. 注册驱动
            Driver driver = new com.mysql.cj.jdbc.Driver();//多态，父类型引用指向子类型对象
            DriverManager.registerDriver(driver);

            //2. 获取连接
            /*
                url:统一资源定位符（网络资源绝对路径）
                包括：
                    http://36.152.44.95:80/index.html
                    协议        IP      port   资源名

             */
            String url = "jdbc:mysql://127.0.0.1:3306/bjpowernode?serverTimezone=GMT";
            //localhost和 127.0.0.1都是本机IP
            String user = "root";
            String password = "hqw3.1415926";
            conn = DriverManager.getConnection(url,user,password);
            System.out.println("数据库连接对象=" + conn);

            //3. 获取数据库操作对象(Statement专门执行sql)
            stmt = conn.createStatement();

            //4. 执行sql
            String sql = "insert into dept(deptno,dname,loc) values(50,'人事部','北京')";
            //专门执行DML语句的(insert delete update)
            //返回值时影响数据库的记录条数
            int count = stmt.executeUpdate(sql);
            System.out.println(count ==1 ? "保存成功":"保存失败");
            //5. 处理查询结果集

        }catch (SQLException e){
            e.printStackTrace();

        }finally{
            //6. 释放资源
            //未来保证资源释放，在语句块中关闭资源
            //并且从小到大依次关闭
            if(stmt!= null){
                try {
                    stmt.close();
                } catch (SQLException t) {
                    t.printStackTrace();
                }
            }
            if (conn != null){
                try {
                    conn.close();
                } catch (SQLException t) {
                    t.printStackTrace();
                }
            }
        }
    }
}
```

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class JDBCTest02 {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stat = null;
        try{
            //1.注册驱动
            DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());
            //2.获取连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode?serverTimezone=GMT","root","hqw3.1415926");
            //3.获取数据库操作对象
            stat = conn.createStatement();
            //4.执行SQL语句
            //java中sql语句不写分号
            String sql = "delete from dept where deptno = 50";
            int count = stat.executeUpdate(sql);
            System.out.println(count == 1 ? "删除成功" : "删除失败");

        }catch (SQLException e){
            e.printStackTrace();
        }finally{
            //5.释放连接
            if (stat != null){
                try {
                    stat.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            if (conn != null){
                try {
                    conn.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
    }
}
```

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ResourceBundle;

public class JDBCTest03 {
    public static void main(String[] args) {
        //使用资源绑定器绑定属性配置文件
        ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
        String driver = bundle.getString("driver");
        String url = bundle.getString("url");
        String user = bundle.getString("user");
        String password = bundle.getString("password");

        Connection conn = null;
        Statement stat = null;
        try {
            //1.注册驱动
            //第一种方法：DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());
            //第二种常用：
            Class.forName(driver);
            //2.获取连接
            conn = DriverManager.getConnection(url,user,password);
            //3.获取数据库操作对象
            stat = conn.createStatement();
            //4.执行SQL语句
            String sql = "insert into dept(deptno,dname,loc) values(50,'人事部','北京')";
            //专门执行DML语句的(insert delete update)
            //返回值时影响数据库的记录条数
            int count = stat.executeUpdate(sql);
            System.out.println(count ==1 ? "保存成功":"保存失败");

        } catch (SQLException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```
* 模拟用户登录功能

```
import java.sql.*;
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

public class JDBCTest06 {
    public static void main(String[] args) {
        //初始化一个界面
        Map<String,String> userLoginInfo = initUI();
        //验证用户名和密码
        boolean loginSuccess = login(userLoginInfo);
        //输出结果
        System.out.println((loginSuccess ? "登录成功" : "登录失败"));
    }

    /**
     * 用户登录
     * @param userLoginInfo 用户登录信息
     * @return false表示失败 true成功
     */
    private static boolean login(Map<String, String> userLoginInfo) {
        //打标记
        boolean loginSuccess = false;
        //单独定义变量
        String loginName = userLoginInfo.get("loginName");
        String loginPwd = userLoginInfo.get("loginPwd");
        //JDBC代码
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            //1.注册驱动
            Class.forName("com.mysql.cj.jdbc.Driver");

            //2.获取连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode?serverTimezone=GMT","root","hqw3.1415926");

            //3.获取数据操作对象
            stmt = conn.createStatement();

            //4.执行sql
            String sql = "select * from t_user where loginName = '"+loginName+"' and loginPwd = '"+loginPwd+"' ";
            rs = stmt.executeQuery(sql);
            //5.处理结果集
            if (rs.next()){
                //登录成功
                loginSuccess = true;
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            //6.释放资源
            if (rs != null){
                try {
                    rs.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            if (stmt != null){
                try {
                    stmt.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            if (conn != null){
                try {
                    conn.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
        return loginSuccess;
    }

    /**
     * 初始化用户信息
     * @return 用户输入用户名和密码登录信息
     */
    private static Map<String,String> initUI() {
        Scanner s = new Scanner(System.in);
        System.out.println("用户名：");
        String loginName = s.nextLine();
        System.out.println("密码：");
        String loginPwd = s.nextLine();
        Map<String,String> userLoginInfo = new HashMap<>();
        userLoginInfo.put("loginName",loginName);
        userLoginInfo.put("loginPwd",loginPwd);
        return userLoginInfo;

    }
}

```
**上面案例存在sql注入**

用户输入信息含有sql关键字，并且这些关键字参与sql语句编译，导致sql语句的原意被扭曲，进而达到sql注入

解决sql注入：

* PreparedStatement接口继承了java.sql.Statement，是属于预编译的数据库对象。原理是对sql框架预编译
* PreparedStatement编译一次执行n次
* PreparedStatement会在编译阶段做类型的安全检查

```
public class JDBCTest07 {
    public static void main(String[] args) {
        //初始化一个界面
        Map<String,String> userLoginInfo = initUI();
        //验证用户名和密码
        boolean loginSuccess = login(userLoginInfo);
        //输出结果
        System.out.println((loginSuccess ? "登录成功" : "登录失败"));
    }

    /**
     * 用户登录
     * @param userLoginInfo 用户登录信息
     * @return false表示失败 true成功
     */
    private static boolean login(Map<String, String> userLoginInfo) {
        //打标记
        boolean loginSuccess = false;
        //单独定义变量
        String loginName = userLoginInfo.get("loginName");
        String loginPwd = userLoginInfo.get("loginPwd");
        //JDBC代码
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;

        try {
            //1.注册驱动
            Class.forName("com.mysql.cj.jdbc.Driver");

            //2.获取连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode?serverTimezone=GMT","root","hqw3.1415926");

            //3.获取预编译的数据操作对象
            //问号为占位符,?将来接收一个值
            String sql = "select * from t_user where loginName = ? and loginPwd = ?";
            //sql语句发给DBMS进行预编译
            ps = conn.prepareStatement(sql);
            //给占位符传值（第一个?下标为1，JDBC下标从1开始）
            ps.setString(1,loginName);
            ps.setString(2,loginPwd);

            //4.执行sql
            rs = ps.executeQuery();

            //5.处理结果集
            if (rs.next()){
                //登录成功
                loginSuccess = true;
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            //6.释放资源
            if (rs != null){
                try {
                    rs.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            if (ps != null){
                try {
                    ps.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            if (conn != null){
                try {
                    conn.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
        return loginSuccess;
    }

    /**
     * 初始化用户信息
     * @return 用户输入用户名和密码登录信息
     */
    private static Map<String,String> initUI() {
        Scanner s = new Scanner(System.in);
        System.out.println("用户名：");
        String loginName = s.nextLine();
        System.out.println("密码：");
        String loginPwd = s.nextLine();
        Map<String,String> userLoginInfo = new HashMap<>();
        userLoginInfo.put("loginName",loginName);
        userLoginInfo.put("loginPwd",loginPwd);
        return userLoginInfo;

    }
}
```

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class JDBCTest09 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;

        try {
            //注册驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //获取连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode?serverTimezone=GMT","root","hqw3.1415926");
            //获取预编译的数据库对象
            String sql = "insert into dept(deptno,dname,loc) values (?,?,?)";
            ps = conn.prepareStatement(sql);
            ps.setInt(1,60);
            ps.setString(2,"销售部");
            ps.setString(3,"上海");

            //执行sql
            int count = ps.executeUpdate();
            System.out.println(count);

        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            if (ps != null){
                try {
                    ps.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            if (conn != null){
                try {
                    conn.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
    }
}

```
* JDBC事务机制

JDBC中只要完成一个DML语句就提交一次

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

/**
 * sql脚本：
 *      drop table if exists t_act;
 *      create table t_act(
 *              actno bigint,
 *              balance double(7,2)
 *      );
 *      insert into t_act(actno,balance) values (111,20000);
 *      insert into t_act(actno,balance) values (222,0);
 *      commit;
 *      select * from t_act;
 */
public class JDBCTest10 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode","root","hqw3.1415926");

            //将自动提交机制调整为手动提交,开启事务
            conn.setAutoCommit(false);

            String sql = "update t_act set balance = ? where actno = ?";
            ps = conn.prepareStatement(sql);

            ps.setDouble(1,10000);
            ps.setDouble(2,111);
            int count = ps.executeUpdate();
            ps.setDouble(1,10000);
            ps.setDouble(2,222);
            count += ps.executeUpdate();

            System.out.println(count ==2 ? "转账成功" : "转账失败");

            //程序能走到这说明没有异常，事务结束，手动提交
            conn.commit();

        } catch (ClassNotFoundException e) {
            //回滚事务
            if (conn != null) {
                try {
                    conn.rollback();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            if (ps != null){
                try {
                    ps.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            if (conn != null){
                try {
                    conn.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
    }
}

```
自建工具类并实现模糊查询

```
import java.sql.*;

public class DBUtil {
    /**
     * 工具类中的构造方法都是私有的
     * 因为工具类中的方法是静态的，不需要new对象，直接采用类名调用
     */
    private DBUtil(){}

    //静态代码块在类加载时只执行一次
    static {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取数据库连接对象
     * @return 连接对象
     * @throws SQLException
     */
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode?serverTimezone=GMT","root","hqw3.1415926");
    }

    /**
     * 关闭资源
     * @param conn 连接对象
     * @param ps 数据库操作对象
     * @param rs 结果集
     */
    public static void close(Connection conn, Statement ps, ResultSet rs){
        if (rs != null){
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (ps != null){
            try {
                ps.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (conn != null){
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}

```

```
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class JDBCTest12 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            //获取连接
            conn = DBUtil.getConnection();
            //获取预编译的数据库操作对象
            /*
            String sql = "select ename from emp where ename like '_?%'";
            ps = conn.prepareStatement(sql);
            ps.setString(1,"A");
            错误写法
             */
            String sql = "select ename from emp where ename like ?";
            ps = conn.prepareStatement(sql);
            ps.setString(1,"_A%");
            rs = ps.executeQuery();

            while (rs.next()){
                System.out.println(rs.getString("ename"));
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally{
            DBUtil.close(conn,ps,rs);
        }
    }
}
```
