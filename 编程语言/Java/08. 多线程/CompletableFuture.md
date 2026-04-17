### CompletableFuture

Future 在实际使用过程中存在一些局限性比如不支持异步任务的编排组合、获取计算结果的 get() 方法为阻塞调用。

Java 8 才被引入CompletableFuture 类可以解决Future 的这些缺陷。CompletableFuture 除了提供了更为好用和强大的 Future 特性之外，还提供了函数式编程、异步任务编排组合（可以将多个异步任务串联起来，组成一个完整的链式调用）等能力。

下面我们来简单看看 CompletableFuture 类的定义。

```java
public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
}
```
可以看到，CompletableFuture 同时实现了 Future 和 CompletionStage 接口。

