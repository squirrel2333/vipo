
简单回顾一下 HashMap **(JDK1.7)** 的结构：

![](ConcurrentHashMap_001.png)

在 JDK7 下，高并发时，有可能出现下面的环形链表：

![](ConcurrentHashMap_002.png)

JDK1.8中改为尾插法,但依旧可能发生Node的覆盖问题

要避免 HashMap 的线程安全问题，有多个解决方法，比如改用 HashTable 或者 Collections.synchronizedMap() 方法。

但是这两者都有一个问题，就是性能，无论读还是写，他们两个都会给整个集合加锁，导致同一时间的其他操作阻塞。

ConcurrentHashMap 的优势在于兼顾性能和线程安全，一个线程进行写操作时，它会锁住一小部分，其他部分的读写不受影响，其他线程访问没上锁的地方不会被阻塞。

# 什么是ConcurrentHashMap(JDK1.8之前)

java.util.concurrent.ConcurrentHashMap属于 JUC 包下的一个集合类，可以实现线程安全。

它由多个 Segment 组合而成。Segment 本身就相当于一个 HashMap 对象。同 HashMap 一样，Segment 包含一个 HashEntry 数组，数组中的每一个 HashEntry 既是一个键值对，也是一个链表的头节点。

单一的 Segment 结构如下：

![](ConcurrentHashMap_003.png)

像这样的 Segment 对象，在 ConcurrentHashMap 集合中有多少个呢？有 2 的 N 次方个，共同保存在一个名为 segments 的数组当中。

因此整个ConcurrentHashMap的结构如下：

![](ConcurrentHashMap_004.png)

可以说，ConcurrentHashMap 是一个二级哈希表。在一个总的哈希表下面，有若干个子哈希表。

**高并发线程安全**

Put 操作时，锁的是某个 Segment，其他线程对其他 Segment 的读写操作均不影响。因此解决了线程安全问题。

# JDK8之后

到了 JDK1.8 的时候，ConcurrentHashMap 已经摒弃了 Segment 的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作。（JDK1.6 以后 synchronized 锁做了很多优化） 整个看起来就像是优化过且线程安全的 HashMap，虽然在 JDK1.8 中还能看到 Segment 的数据结构，但是已经简化了属性，只是为了兼容旧版本；

![](ConcurrentHashMap_005.png)

JDK1.8 的 ConcurrentHashMap 不再是 **Segment 数组 + HashEntry 数组 + 链表**，而是 **Node 数组 + 链表 / 红黑树**。不过，Node 只能用于链表的情况，红黑树的情况需要使用 **TreeNode**。当冲突链表达到一定长度时，链表会转换成红黑树。

TreeNode是存储红黑树节点，被TreeBin包装。TreeBin通过root属性维护红黑树的根结点，因为红黑树在旋转的时候，根结点可能会被它原来的子节点替换掉，在这个时间点，如果有其他线程要写这棵红黑树就会发生线程不安全问题，所以在 ConcurrentHashMap 中TreeBin通过waiter属性维护当前使用这棵红黑树的线程，来防止其他线程的进入。

**Java 8 中，锁粒度更细，synchronized 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，就不会影响其他 Node 的读写，效率大幅提升。**

# 复合操作

ConcurrentHashMap 是线程安全的，意味着它可以保证多个线程同时对它进行读写操作时，不会出现数据不一致的情况，也不会导致 JDK1.7 及之前版本的 HashMap 多线程操作导致死循环问题。但是，这并不意味着它可以保证所有的复合操作都是原子性的，一定不要搞混了！

复合操作是指由多个基本操作(如put、get、remove、containsKey等)组成的操作，例如先判断某个键是否存在containsKey(key)，然后根据结果进行插入或更新put(key, value)。这种操作在执行过程中可能会被其他线程打断，导致结果不符合预期。

例如，有两个线程 A 和 B 同时对 ConcurrentHashMap 进行复合操作，如下：

```java
// 线程 A
if (!map.containsKey(key)) {
map.put(key, value);
}
// 线程 B
if (!map.containsKey(key)) {
map.put(key, anotherValue);
}
```
如果线程 A 和 B 的执行顺序是这样：

1. 线程 A 判断 map 中不存在 key
2. 线程 B 判断 map 中不存在 key
3. 线程 B 将 (key, anotherValue) 插入 map
4. 线程 A 将 (key, value) 插入 map

那么最终的结果是 (key, value)，而不是预期的 (key, anotherValue)。这就是复合操作的非原子性导致的问题。

**那如何保证** **ConcurrentHashMap** **复合操作的原子性呢？**

ConcurrentHashMap 提供了一些原子性的复合操作，如 putIfAbsent、compute、computeIfAbsent 、computeIfPresent、merge等。这些方法都可以接受一个函数作为参数，根据给定的 key 和 value 来计算一个新的 value，并且将其更新到 map 中。

上面的代码可以改写为：

```java
// 线程 A
map.putIfAbsent(key, value);
// 线程 B
map.putIfAbsent(key, anotherValue);
```
或者：

```java
// 线程 A
map.computeIfAbsent(key, k -> value);
// 线程 B
map.computeIfAbsent(key, k -> anotherValue);
```

