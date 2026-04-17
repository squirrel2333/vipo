
看scala的源码的话很发现很多源码开头都有一句：self => 这句相当于给this起了一个别名为self

```
class A {
    self =>  //this别名
    val x=2
    def foo = self.x + this.x
}
```
self不是关键字，可以用除了this外的任何名字命名(除关键字)。就上面的代码，在A内部，可以用this指代当前对象，也可以用self指代，两者是等价的。

---

```
class Outer { outer =>
    val v1 = "here"
    class Inner {
        println(outer.v1) // 用outer表示外部类，相当于Outer.this
    }
}
```
对于this别名 self =>这种写法形式，是**自身类型(self type)**的一种特殊方式。self在**不声明类型的情况下**，只是this的别名，所以不允许用this做this的别名`class C { this => } //error 不能用this做别名`

但当声明了类型的时候，就不是别名的用途了，这个时候表示**自身类型**，比如：

`scala> class C { this:X => }`

**this:X => 要求C在实例化时或定义C的子类时，必须混入指定的X类型.**

这个X类型也可以指定为当前类型`scala> class C { this:C => } // 不会报错`

在定义C的子类时，因为自身类型的约束，也必须满足X类型，**即子类必须也混入X**

---

如果自身类型是定义在特质中（大多情况下）：

`trait T { this:X => }`

那么当某个class或object 要继承或混入 T 时，必须也要满足 X 类型，如果该类/单例不是X的子类的话就要同时混入X才可以 `object A extends T with X`

---

自身类型也可以声明为复合类型

`this: X with Y with Z =>` 或`this: { def close:Unit} =>`

![](scala_self_自类型_001.png)

