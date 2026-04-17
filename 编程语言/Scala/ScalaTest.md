# JUnit

## Before/After

```
import org.junit.Assert._
import org.junit._

class UsingJUnit {

  @Before
  def before(): Unit = {
    println("before test")
  }

  @After
  def after(): Unit = {
    println("after test")
  }

  @Test
  def testList(): Unit = {
    println("testList")
    val list = List("a", "b")

    assertEquals(List("a", "b"), list)
    assertNotEquals(List("b", "a"), list)
  }
}
```
与Java保持一致

## BeforeClass/AfterClass

```
object UsingJUnit {
  @BeforeClass
  def beforeClass(): Unit = {
    println("before class")
  }

  @AfterClass
  def afterClass(): Unit = {
    println("after class")
  }
}
```
Java中必须是static方法，对应scala必须写在object里

## 异常处理

```
import org.junit.Assert._
import org.junit._

class JunitCheckException {

  val _thrown = rules.ExpectedException.none

  @Rule
  def thrown = _thrown

  @Test(expected = classOf[IndexOutOfBoundsException])
  def testStringIndexOutOfBounds(): Unit = {
    val s = "test string"
    s.charAt(-1)
  }

  @Test
  def testStringIndexOutOfBoundsExceptionMessage(): Unit = {
    val s = "test string"
    thrown.expect(classOf[IndexOutOfBoundsException])
    thrown.expectMessage("String index out of range: -1")
    s.charAt(-1)
  }
}
```
我们在编写代码的时候，会预期抛出一些异常，对这些异常的检查，也是单测中需要做的事情。下面举例，说明异常检测的方法，一种只检查抛出的异常类，另外一种是检查异常类的类型和 Message 信息。

Scala 在使用 JUnit @Rule 的时候有些问题，一定需要是 public 才可以，下面是一个变通的实现方式。

