
Java8 中增加了 Stream 处理，可以配合 Lambda 表达式来使用，让操作集合非常便利。虽然我们平时经常使用 Stream，但用到的方法其实并不多，梳理清楚之后还是非常好掌握的。

![](JavaStream_001.png)

* 无状态操作：当前元素的操作不受前面元素的影响
* 有状态操作：当前操作需要等待所有元素处理完才能进行
* 短路操作：不需要处理完全部元素就可以结束
* 非短路操作：必须处理完所有元素才能结束

## 创建Stream

### 使用集合创建

```java
List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5);
Stream stream = list1.stream();
```
### 使用数组创建

```java
String[] array = {"a", "b", "c"};
Stream stream2 = Arrays.stream(array);
```
### 使用Stream静态方法创建

```java
Stream<String> stream3 = Stream.of("ab", "cd", "ef");
Stream<Integer> stream4 = Stream.iterate(0, (x) -> x + 3).limit(5);
Stream<Integer> stream5 = Stream.generate(new Random()::nextInt).limit(3);

stream3.forEach(r -> System.out.print(r + " "));
stream4.forEach(r -> System.out.print(r + " "));
stream5.forEach(r -> System.out.print(r + " "));
```
## 无状态操作

![](JavaStream_002.png)

### map

接收一个函数作为入参，把这个函数应用到每个元素上，执行结果组成一个新的 stream 返回。

![](JavaStream_003.png)

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
List<Integer> newList = list.stream().map(x -> x + 3).collect(Collectors.toList());
System.out.println("newList:" + newList);
```

```java
List<String> list = Arrays.asList("ab", "abc", "abcd", "abcde", "abcdef");
List<String> newList = list.stream().map(String::toUpperCase).collect(Collectors.toList());
System.out.println("newList:" + newList);
```
### mapTo

`mapToInt``mapToDouble``mapToLong`

```java
List<String> list = Arrays.asList("ab", "abc", "abcd", "abcde", "abcdef");
int[] newList = list.stream().mapToInt(r -> r.length()).toArray();
System.out.println("newList:" + Arrays.toString(newList));
```
### flatMap

flatMap接收函数作为入参，然后把集合中每个元素转换成一个 stream，再把这些 stream 组成一个新的 stream，是拆分单词很好的工具。

![](JavaStream_004.png)

```java
List<String> list = Arrays.asList("ab-abc-abcd-abcde-abcdef", "5-2-3-1-4");
List<String> newList = list.stream().flatMap(s -> Arrays.stream(s.split("-"))).collect(Collectors.toList());
System.out.println("newList：" + newList);
```
### flatMapTo

`flatMapToInt``flatMapToLong``flatMapToDouble`

```java
int[][] data = {{1,2},{3,4},{5,6}};
IntStream intStream = Arrays.stream(data).flatMapToInt(row -> Arrays.stream(row));
System.out.println(intStream.sum()); // 21
```
### filter

筛选功能，按照一定的规则将符合条件的元素提取到新的流中。

```java
List<String> nameList = students.stream().filter(x -> x.getScore() >= 90)
    .map(Student::getName).collect(Collectors.toList());
System.out.print("考试成绩90分以上的学生姓名：" + nameList);
```
### peek

返回由 stream 中元素组成的新 stream，用给定的函数作用在新 stream 的每个元素上。传入的函数是一个 Consume 类型的，没有返回值，因此并不会改变原 stream 中元素的值。peek 主要用是 debug，可以方便地 查看流处理结果是否正确。

```java
Stream.of("one", "two", "three", "four")
             .filter(e -> e.length() > 3)
             .peek(e -> System.out.println("Filtered value: " + e))
             .map(String::toUpperCase)
             .peek(e -> System.out.println("Mapped value: " + e))
             .collect(Collectors.toList());
```
### unordered

把一个有序的 stream 转成一个无序 stream ，如果原 stream 本身就是无序的，可能会返回原始的 stream。

```java
Arrays.asList("1", "2", "3", "4", "5")
                .parallelStream()
                .unordered()
                .forEach(r -> System.out.print(r + " "));
```
## 有状态操作

![](JavaStream_005.png)

### distinct

去重

```java
String[] array = { "a", "b", "b", "c", "c", "d", "d", "e", "e"};
List<String> newList = Arrays.stream(array).distinct().collect(Collectors.toList());
System.out.println("newList:" + newList);
```
### limit

stream中获取前n个元素

```java
String[] array = { "c", "c", "a", "b", "b", "e", "e", "d", "d"};
List<String> newList = Arrays.stream(array).limit(5).collect(Collectors.toList());
System.out.println("newList:" + newList);
```
### skip

跳过Stream中前n个元素

```java
String[] array = { "a", "b", "c", "d", "e", "f", "g", "h", "i"};
List<String> newList = Arrays.stream(array).skip(5).collect(Collectors.toList());
System.out.println("newList:" + newList);
```
### sorted

排序功能

```java
String[] array = { "c", "c", "a", "b", "b", "e", "e", "d", "d"};
List<String> newList = Arrays.stream(array).sorted().collect(Collectors.toList());
System.out.println("newList:" + newList);
// 输出：newList:[a, b, b, c, c, d, d, e, e]
```

```java
List<String> nameList = students.stream()
    .sorted(Comparator.comparing(Student::getScore))
    .map(Student::getName)
    .collect(Collectors.toList());
System.out.print("按成绩排序输出学生姓名：" + nameList);
```
## 短路操作

![](JavaStream_006.png)

### findAny

找出 stream 中任何一个满足过滤条件的元素。

```java
Optional<Student> studentFindAny = students.stream().
    filter(x -> x.getScore() > 90).findAny();
System.out.print("找出任意一个考试成绩在90分以上的学生姓名：" + studentFindAny.orElseGet(null).getName());
```
### anyMatch

是否存在任意一个满足给定条件的元素

```java
boolean result1 = students.stream().anyMatch(x -> x.getScore() > 90);
System.out.println("是否存在成绩高于 90 分的学生：" + result1);
```
### allMatch

是否集合中所有元素都满足给定的条件，如果集合是空，则返回 true。

```java
boolean result1 = students.stream().allMatch(x -> x.getScore() > 90);
System.out.println("是否所有学生的成绩都高于90分：" + result1);
```
### noneMatch

是否没有元素能匹配给定的条件，如果集合是空，则返回 true。

```java
boolean result1 = students.stream().noneMatch(x -> x.getScore() > 90);
System.out.println("是不是没有学生成绩在 90 分以上：" + result1);
```
### findFirst

找出第一个符合条件的元素

```java
Optional<Student> studentFindAny = students.stream()
    .filter(x -> x.getScore() > 90).findFirst();
System.out.print("第一个成绩在 90 分以上的学生姓名：" + studentFindAny.orElseGet(null).getName());
```
## 非短路操作

![](JavaStream_007.png)

### forEach

遍历元素。

```java
List<Integer> array = Arrays.asList(5, 2, 3, 1, 4);
array.stream().forEach(System.out :: println);
```
### forEachOrdered

按照给定集合中元素的顺序输出。主要使用场景是在并行流的情况下，按照给定的顺序输出元素

```java
List<Integer> array = Arrays.asList(5, 2, 3, 1, 4);
array.parallelStream().forEachOrdered(System.out :: println);
```
### toArray

返回包括给定 stream 中所有元素的数组。

```java
Stream<String> stream = Arrays.asList("ab", "abc", "abcd", "abcde", "abcdef").stream();
String[] newArray1 = stream.toArray(str -> new String[5]);
String[] newArray2 = stream.toArray(String[]::new);
Object[] newArray3 = stream.toArray();
```
### reduce

规约操作，把一个流的所有元素合并成一个元素，比如求和、求乘积、求最大最小值等。

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
Optional<Integer> sum = list.stream().reduce((x, y) -> x + y);
Optional<Integer> product = list.stream().reduce((x, y) -> x * y);
Optional<Integer> max = list.stream().reduce((x, y) -> x > y ? x : y);
System.out.println("数组元素之和：" + sum.get());
System.out.println("数组元素乘积：" + product.get());
System.out.println("数组元素最大值：" + max.get());
```

```java
Optional<Integer> maxScore = students.stream().map(r -> r.getScore()).reduce(Integer::max);
Optional<Integer> sumScore = students.stream().map(r -> r.getScore()).reduce(Integer::sum);
System.out.println("全班学生最高分：" + maxScore.get());
System.out.println("全班学生总分：" + sumScore.get());
```
### collect

![](JavaStream_008.png)

把 stream 中的元素归集到新的集合或者归集成单个元素。

#### 归集为集合

`toList``toSet``toMap`

```java
List<String> list = students.stream().map(r -> r.getName()).collect(Collectors.toList());
Set<Integer> set = students.stream().map(r -> r.getScore()).collect(Collectors.toSet());
Map<String, Integer> map = students.stream().collect(Collectors.toMap(Student::getName, Student::getScore));
System.out.println("全班学生姓名列表：" + list);
System.out.println("全班学生不同分数列表：" + set);
System.out.println("全班学生姓名分数集合：" + map);
```
#### 统计功能

![](JavaStream_009.png)

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
long count = list.stream().collect(Collectors.counting());
int sum = list.stream().collect(Collectors.summingInt(r -> r));
double average = list.stream().collect(Collectors.averagingDouble(r -> r));
Optional<Integer> max = list.stream().collect(Collectors.maxBy(Integer::compare));
Optional<Integer> min = list.stream().collect(Collectors.maxBy((x, y) -> x > y ? y : x));
```

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
IntSummaryStatistics statistics = list.stream().collect(Collectors.summarizingInt(r -> r));
System.out.println("综合统计结果：" + statistics.toString());
```
#### 分区和分组

* `partitioningBy`把stream分成两个map
* `groupingBy`把stream分成多个map

```java
Map<Boolean, List<Student>> partitionByScore = students.stream().collect(Collectors.partitioningBy(x -> x.getScore() > 80));
System.out.println("将学生按照考试成绩80分以上分区：");
partitionByScore.forEach((k,v ) -> {
    System.out.print(k ? "80分以上：" : "80分以下：");
    v.forEach(r -> System.out.print(r.getName() + ","));
    System.out.println();
});
```

```java
Map<String, Map<Integer, List<Student>>> group = students.stream().collect(Collectors.groupingBy(Student::getSex, Collectors.groupingBy(Student::getAge)));
System.out.println("将学生按照性别、年龄分组：");
group.forEach((k,v ) -> {
    System.out.println(k +"：");
    v.forEach((k1,v1) -> {
        System.out.print("      " + k1 + ":" );
        v1.forEach(r -> System.out.print(r.getName() + ","));
        System.out.println();
    });
});
```
#### 连接

将stream中的元素用指定的连接符合并，连接符可以是空

```java
String studentNames = students.stream().map(r -> r.getName()).collect(Collectors.joining(","));
System.out.println("所有学生姓名列表：" + studentNames);
```
#### 规约

这里支持更强大的自定义规约

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
int listSum = list.stream().collect(Collectors.reducing(0, x -> x + 1, (sum, b) -> sum + b));
System.out.println("数组中每个元素加 1 后总和：" + listSum);
```
### max、min、count

stream提供方便统计的方法

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
System.out.println("数组元素最大值："+list.stream().max(Integer::compareTo).get());
System.out.println("数组元素最小值："+list.stream().min(Integer::compareTo).get());
System.out.println("数组中大于3的元素个数："+list.stream().filter(x -> x > 3).count());
```

```java
Optional<Student> optional = students.stream().max(Comparator.comparing(r -> r.getScore()));
System.out.println("成绩最高的学生姓名：" + optional.get().getName());
```
