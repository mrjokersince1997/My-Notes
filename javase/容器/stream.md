# 流 Stream

---

## 基础概念

### 流

流处理是对运动中的数据的处理，在生成或接收数据时直接计算数据。应用程序中分析和查询不断存在，数据不断地流经它们。在从流中接收到事件时，流处理应用程序对该事件作出反应。

流处理可以立即对事件做出反应，且可以处理比其他数据处理系统大得多的数据量：直接处理事件流，并且只保留数据中有意义的子集。尤其是面对持续生成，本质上是无穷尽的数据集。

### Java Stream 类

JDK 1.8 新增。将要处理的元素集合看作一种流，在管道的节点上进行处理。使代码更简洁易读。

```java
List<Integer> transactionsIds = 
widgets.stream()                                            // 声明作为流处理
       .filter(b -> b.getColor() == RED)                    // 过滤
       .sorted((x,y) -> x.getWeight() - y.getWeight())      // 排序
       .mapToInt(Widget::getWeight)                         // 转化
       .sum();                                              // 汇总
```


---

## 流操作

### 生成流 stream

集合接口有两个方法来生成流，数据类型将由 List 转化为 Stream 。

- `stream` 方法：为集合创建串行流。
- `parallelStream` 方法：为集合创建并行流。

```java
// 获取空字符串的数量
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
int count = strings.parallelStream().filter(string -> string.isEmpty()).count();
```


Stream操作分类
中间操作(Intermediate operations)	无状态(Stateless)	unordered() filter() map() mapToInt() mapToLong() mapToDouble() flatMap() flatMapToInt() flatMapToLong() flatMapToDouble() peek()
有状态(Stateful)	distinct() sorted() sorted() limit() skip()
结束操作(Terminal operations)	非短路操作	forEach() forEachOrdered() toArray() reduce() collect() max() min() count()
短路操作(short-circuiting)	anyMatch() allMatch() noneMatch() findFirst() findAny()

Stream上的所有操作分为两类：中间操作和结束操作，中间操作只是一种标记，只有结束操作才会触发实际计算。中间操作又可以分为无状态的(Stateless)和有状态的(Stateful)，无状态中间操作是指元素的处理不受前面元素的影响，而有状态的中间操作必须等到所有元素处理之后才知道最终结果，比如排序是有状态操作，在读取所有元素之前并不能确定排序结果；结束操作又可以分为短路操作和非短路操作，短路操作是指不用处理全部元素就可以返回结果，比如找到第一个满足条件的元素。之所以要进行如此精细的划分，是因为底层对每一种情况的处理方式不同。

实际上Stream API内部实现的的本质，就是如何重载Sink的这四个接口方法。（底层都是 sink 方法）



### 映射 map

`map` 方法用于映射每个元素到对应的结果，其实就是对结果进行转化。

```java
// 获取对应的平方数
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
```

### 过滤 filter

`filter` 方法用于通过设置的条件过滤出元素。

List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 获取空字符串的数量
long count = strings.stream().filter(string -> string.isEmpty()).count();



### 筛选 limit 

`limit` 方法用于获取指定数量的流。 

```java
// 筛选出 10 条数据
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

### 排序 sorted

`sorted` 方法用于对流进行排序。以下代码片段使用 sorted 方法对输出的 10 个随机数进行排序：

```java
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
```


### 迭代 forEach

结束操作： `forEach` 迭代流中的每个数据，即对每个数据进行最后的处理（比如保存到数据库中或打印）。

```java
// 输出 10 个随机数 
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

### 聚合 Collectors

结束操作：`Collectors` 类实现了归约操作，例如将流转换成集合和聚合元素，可用于返回列表或字符串。

```java
// Stream 转化为 List
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream()
                               .filter(string -> !string.isEmpty())
                               .collect(Collectors.toList()); 
System.out.println("筛选列表: " + filtered);

// Stream 转化为 String
String mergedString = strings.stream()
                             .filter(string -> !string.isEmpty())
                             .collect(Collectors.joining(", "));
System.out.println("合并字符串: " + mergedString);
```


### 统计 SummaryStatistics 

结束操作：收集最终产生的统计结果，它们主要用于 int、double、long 等基本类型上。

```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
 
IntSummaryStatistics stats = numbers.stream()
                                    .mapToInt((x) -> x)
                                    .summaryStatistics();
 
System.out.println("列表中最大的数 : " + stats.getMax());
System.out.println("列表中最小的数 : " + stats.getMin());
System.out.println("所有数之和 : " + stats.getSum());
System.out.println("平均数 : " + stats.getAverage());
```
