
# 简介

## 单元测试

使用main()方法测试的缺点：

* 只能有一个main方法，不能把测试代码分离
* 没有打印出测试结果和期望结果

单元测试好处：

* 确保单个方法运行正常
* 如果修改了方法代码，只需要确保其对应的单元测试通过
* 测试代码本身可以作为示例代码
* 可以自动化运行所有测试并获得报告

## JUnit

开源Java语言单元测试框架

* 专门针对Java语言设计
* 事实上的标准单元测试框架

### 特点

* 使用断言（Assertion）测试期望结果
* 方便的组织和运行测试
* 方便的查看测试结果
* 常用IDE都集成了JUnit
* 方便集成到Maven

### 设计

* TestCase：一个TestCase表示一个测试
* TestSuite：一个TestSuite包含一组TestCase，表示一组测试
* TestFixture：一个TestFixture表示一个测试环境
* TestResult：用于收集测试结果
* TestRunner：用于运行测试
* TestListener：用于监听测试过程
* Assert：用于断言测试结果是否正确

### Demo

```java
import com.hqw.util.Calculator;
import org.junit.Test;

import static org.junit.Assert.*;

public class CalculateSuite {

    @Test
    public void testCalculate() {
        assertEquals(3, new Calculator().calculate(1,2));
    }

    @Test
    public void testCalculate2() {
        assertEquals(4, new Calculator().calculate(2, 1));
    }

}

/*
java.lang.AssertionError:
Expected :4
Actual   :3
*/
```
### 使用断言

* 断言相等： `assertEquals(100, x)`
* 断言数组相等：`assertArrayEquals({1, 2, 3}, x)`
* 浮点数断言相等：`assertEquals(3.14, x, 0.01)`
* 断言为null：`assertNull(x)`
* 断言为true/false: `assertTrue(x > 0) assertFalse(x < 0)`
* 其它：`assertNotEquals assertNotNull`

### 事项

* 测试方法应该完全独立
* 测试代码必须简单
* 不能为测试编写测试
* 测试要涵盖各种输入条件尤其是边界条件

# Before/After

对应每个@Test方法

1. 实例化CalculateTest
2. 执行@Before方法
3. 执行@Test方法
4. 执行@After方法

使用Before和After保证：

* 单个测试方法执行前会创建新的XXXTest实例。实例变量状态不会传递给下个Test方法
* 单个Test方法执行前后会执行Before和After方法

```java
public class CalculateTest {
    StringBuilder builder;

    @Before
    public void setUp() {
        builder = new StringBuilder();
    }
    ...
}
```
`@BeforeClass`和`@AfterClass`静态方法：

1. `@BeforeClass`在执行所有`@Test`方法前执行
2. 执行测试
3. `@AfterClass`在执行所有`@Test`方法后执行

**注意：**

* `@BeforeClass`静态方法初始化的对象只能存放在静态字段中
* 静态字段会影响到所有的TestCase

![](JUnit_001.png)

初始化测试资源称为Fixture

* @Before: 初始化测试对象，例如`intput = new FileInputStream()`
* @After: 销毁Before创建的资源对象，例如`input.close()`
* @BeforeClass: 初始化非常耗时的资源，例如创建数据库
* @AfterClass: 清理@BeforeClass创建的资源，例如销毁数据库

# 异常测试

对可能抛出的异常进行测试：

* 异常本身是方法前面的一部分
* 测试错误输入是否导致特定的异常

## 使用expected测试异常

```java
@Test(expected = NumberFormatException.class)
public void testNumberFormatException() {
    Integer.parseInt(null);
}
```
* 对可能发生的每种类型的异常都要进行测试

# 参数化测试

