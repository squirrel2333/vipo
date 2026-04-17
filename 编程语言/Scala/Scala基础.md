
```
/*
	object: 关键字，声明一个单例对象（伴生对象）
  Scala为了实现纯面向对象，删除了static关键字，使用object实现static的功能
*/
object HelloScala {
  /*
  	main方法：从外部可以直接调用
    def 方法名称(参数名称：参数类型）：返回值类型 = {方法体}
    不用分号
  */
  def main(args: Array[String]): Unit = {
    println("hello scala");
    // 也可以直接调用java
    System.out.println("hello scala from java");
  }
}
```
编译：

>scalac HelloScala.scala 生成 HelloScala.class 和 HelloScala$.class（存放伴生对象的所属类）

运行：

>scala HelloScala

**main方法还可以通过继承App来实现程序入口**

### 伴生对象

```
class Student(name: String, age: Int) {
  def printInfo(): Unit = {
    //school属性在java中可以设置为Student的static属性，直接用类.的方式调用，但是不够面向对象
    //因此在Scala中，创建伴生对象object（唯一创建）
    print(name + " " + age " " + Student.school)
  }
}

//引入同名伴生对象，可以互相访问其中的各种属性和方法
object Student {
  val school: String = "NJU"

  def main(args: Array[String]): Unit = {
  	var alice = new Student("Alice", "18")
    alice.printInfo()
  }
}
```

