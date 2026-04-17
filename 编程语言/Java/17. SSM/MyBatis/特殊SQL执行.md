# 处理模糊查询

```java
public interface SqlMapper {

    /**
     * 根据用户名模糊查询
     */
    List<User> getUserByLike(@Param("username") String username);
}
```

```java
public class SqlMapperTest {

    @Test
    public void testGetUserByLike() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SqlMapper mapper = sqlSession.getMapper(SqlMapper.class);
        List<User> qw = mapper.getUserByLike("qw");
        System.out.println(qw);
    }
}
```

```java
<mapper namespace="com.hqw.mybatis.mapper.SqlMapper">
    <select id="getUserByLike" resultType="User">
         select * from t_user where username like '%${username}'
         select * from t_user where username like concat('%',#{username},'%')
         select * from t_user where username like "%"#{username}"%"
    </select>
</mapper>
```
第三种方式最推荐

# 批量删除

```java
int deleteMore(@Param("ids") String ids);
```

```java
    <!--int deleteMore(@Param("ids") String ids);-->
    <delete id="deleteMore">
        delete from t_user where id in (${ids})
    </delete>
```

```java
    @Test
    //int deleteMore(@Param("ids") String ids);
    public void testDeleteMore() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        int i = mapper.deleteMore("1,2,3");
        System.out.println(i);
    }
```
* mapper中不能使用`#{}`，因为会自动将参数带上引号。拼接为`delete from t\_user where id in ("1,2,3")`sql执行不成功

# 动态设置表名

```java
List<User> getUserByTableName(@Param("tableName") String tableName);
```

```java
    <select id="getUserByTableName" resultType="User">
        select * from ${tableName}
    </select>
```

```java
    //List<User> getUserByTableName(@Param("tableName") String tableName);
    @Test
    public void testGetUserByTableName() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        System.out.println(mapper.getUserByTableName("t_user"));
    }
```
* sql中表名不能带引号，所以不能用`#{}`

# 获取insert的自增主键

```java
int insertUser(User user);
```

```java
    <!--int insertUser(User user);-->
    <insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
        insert into t_user values (null, #{username}, #{password}, #{age}, #{sex}, #{email})
    </insert>
```

```java
    @Test
    public void testInsertUser() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
        User user = new User(null, "hqw", "123", 12, 'g', "sss.com");
        mapper.insertUser(user);
        System.out.println(user);
    }
```

```java
User{id=8, username='hqw', password='123', age=12, sex=g, email='sss.com'}
```
* `insert`返回值为修改的行数，无法通过返回值来得到想要获取的主键值。
* `keyProperty`将主键值重新赋给指定的属性
* `useGeneratedKeys`设置当前标签中的sql使用了自增主键
