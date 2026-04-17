根据特定条件动态拼装SQL语句

# if标签

* 根据标签中的test属性所对应的表达式决定标签中的内容是否需要拼接到SQL中

```java
List<Emp> getEmpByCondition(Emp emp);
```

```java
    <!--List<Emp> getEmpByCondition(Emp emp);-->
    <select id="getEmpByCondition" resultType="Emp">
        select * from t_emp where 1=1
        <if test="empName != null and empName != ''">
            emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
        <if test="sex != null and age != ''">
            and sex = #{sex}
        </if>
        <if test="email != null and age != ''">
            and email = #{email}
        </if>

    </select>
```

```java
    @Test
    public void testGetEmpByCondition() {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        DynamicSQLMapper mapper = sqlSession.getMapper(DynamicSQLMapper.class);
        List<Emp> empByCondition = mapper.getEmpByCondition(new Emp(null, "ls", 13, "男", "sese"));
        System.out.println(empByCondition);
    }
```
* where 1=1是为了使条件恒成立，避免empName为空时，后面的字段多一个and

# where标签

* 当where标签中有内容时，动态生成关键字，并且将内容前多余的and/or可以自动去掉
* 当where标签中没有内容时，where标签没有任何效果
* where标签不能将内容后面多余的and/or去掉

```java
    <!--List<Emp> getEmpByCondition(Emp emp);-->
    <select id="getEmpByCondition" resultType="Emp">
        select * from t_emp
        <where>
            <if test="empName != null and empName != ''">
                emp_name = #{empName}
            </if>
            <if test="age != null and age != ''">
                and age = #{age}
            </if>
            <if test="sex != null and age != ''">
                and sex = #{sex}
            </if>
            <if test="email != null and age != ''">
                and email = #{email}
            </if>
        </where>

    </select>
```
# trim标签

* 若标签中有内容时：
+ `prefix|suffix`:将trim标签中内容前面或后面添加指定内容
+ `prefixOverrides|suffixOverrides`: 将trim标签中内容前面或后面去掉指定内容
* 若标签中没有内容时trim没有任何效果

```java
    <!--List<Emp> getEmpByCondition(Emp emp);-->
    <select id="getEmpByCondition" resultType="Emp">
        select * from t_emp
        <trim prefix="where" suffixOverrides="and|or">
            <if test="empName != null and empName != ''">
                emp_name = #{empName}
            </if>
            <if test="age != null and age != ''">
                and age = #{age}
            </if>
            <if test="sex != null and age != ''">
                and sex = #{sex}
            </if>
            <if test="email != null and age != ''">
                and email = #{email}
            </if>
        </trim>
    </select>
```
# choose, when, otherwise

* 相当于if...else if...else

```java
    <select id="getEmpListByChoose" resultType="Emp">
        select * from t_emp
        <where>
            <choose>
                <when test="ename != '' and ename != null">
                    ename = #{ename}
                </when>
                <when test="age != '' and age != null">
                    age = #{age}
                </when>
                <when test="sex != '' and sex != null">
                    sex = #{sex}
                </when>
                <when test="email != '' and email != null">
                    email = #{email}
                </when>
                <otherwise>
                    did = 1
                </otherwise>
            </choose>
        </where>
    </select>
```
# foreach

* `collection`设置需要循环的数组或集合
* `item`表示数组中的每一个数据
* `separator`表示循环体之间的分隔符
* `open/close`foreach标签所循环的所有内容的开始/结束符

## 批量删除

```java
    // 通过数组实现批量删除
    int deleteMoreByArray(@Param("eids") Integer[] eids);
```

```java
    <!--int deleteMoreByArray(@Param("eids") Integer[] eids);-->
  	<!-- delete from t_emp where eid in (1, 2, 3);-->
    <delete id="deleteMoreByArray">
        delete from t_emp where eid in
            <foreach collection="eids" item="eid" separator="," open="(" close=")">
                #{eid}
            </foreach>
    </delete>

  	<!-- delete from t_emp where eid in 1 or 2;-->
  	<delete id="deleteMoreByArray">
        delete from t_emp where eid in
            <foreach collection="eids" item="eid" separator="or"
                #{eid}
            </foreach>
    </delete>
```
## 批量添加

```java
int insertMoreByList(@Param("emps") List<Emp> emps);
```

```java
    <!--int insertMoreByList(@Param("emps") List<Emp> emps);-->
    <!--insert into t_emp values (null, ?, ?, ?, ?, null) , (null, ?, ?, ?, ?, null) , (null, ?, ?, ?, ?, null) , (null, ?, ?, ?, ?, null)-->
    <insert id="insertMoreByList">
        insert into t_emp values
        <!-- 此处不能加open="(" close=")" 因为括号需要加在每条数据外。此处加表示只在循环外加括号-->
        <foreach collection="emps" item="emp" separator=",">
            (null, #{emp.empName}, #{emp.age}, #{emp.sex}, #{emp.email}, null)
        </foreach>
    </insert>
```

```java
    //int insertMoreByList(@Param("emps") List<Emp> emps);
    @Test
    public void testInsertMoreByList() {
        DynamicSQLMapper mapper = SqlSessionUtils.getSqlSession().getMapper(DynamicSQLMapper.class);
        Emp emp1 = new Emp(null, "a1", 23, "b", "222");
        Emp emp2 = new Emp(null, "a2", 23, "b", "222");
        Emp emp3 = new Emp(null, "a3", 23, "b", "222");
        Emp emp4 = new Emp(null, "a4", 23, "b", "222");
        List<Emp> list = Arrays.asList(emp1, emp2, emp3, emp4);
        System.out.println(mapper.insertMoreByList(list));
    }
```
# sql标签

```java
<!-- 设置sql片段 -->
<sql id="empColumns">eid,emp_name,age,sex,email</sql>

<!-- 引用sql片段-->
<select id="xxx" resultType="Emp">
    select <include refid="empColumns"></include> from t_emp
</select>
```
