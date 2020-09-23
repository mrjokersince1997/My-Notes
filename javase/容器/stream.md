# 流 Stream

---

## 基础概念

### 流

流处理是对运动中的数据的处理，在生成或接收数据时直接计算数据。应用程序中分析和查询不断存在，数据不断地流经它们。在从流中接收到事件时，流处理应用程序对该事件作出反应。

如果我们使用传统的循环迭代方式对数据集进行复杂计算，常常会带来两个弊端：

1. 迭代次数多，迭代次数跟函数调用的次数相等。
2. 频繁产生中间结果，存储开销无法接受。

流处理可以立即对事件做出反应，且可以处理比其他数据处理系统大得多的数据量：直接处理事件流，并且只保留数据中有意义的子集。尤其是面对持续生成，本质上是无穷尽的数据集。


### Java Stream 类

JDK 1.8 新增。将要处理的元素集合看作一种流，在管道的节点上进行处理。使代码更简洁易读。

集合接口有两个方法来生成流，数据类型将由 Collection 转化为 Stream 。

- `stream` 方法：为集合创建串行流。
- `parallelStream` 方法：为集合创建并行流。

1. Stream 的遍历方式和结果与 Iterator 无差别（便于转化），其优势在于其原型链的设计使得它可以对遍历处理后的数据进行再处理。

2. parallelStream 提供了流的并行处理，底层使用 Fork/Join 框架，简单理解就是多线程异步任务的一种实现。处理过程中会有多个线程处理元素，具体由 JDK 负责管理。不保证有序性。

3. 串行流和并行流之间可以通过 `parallel` 和 `sequential` 方法相互转化。

```java
Stream<Integer> stream = list.stream();                     // 声明作为流处理
ParellerStream<Integer> pStream = stream.parallel();        // 转化为并行流
```

### 流操作

流处理的每个操作阶段都会封装到一个 Sink 接口里，处理数据后再将数据传递给下游的 Sink。

Stream 上的所有操作分为两类：中间操作和结束操作。Stream 是延迟执行的，只有调用到结束操作，才触发整个流水线的执行。如果未定义结束操作，那么流处理什么也不会做。


```java
// 获取空字符串的数量
int count = strings.parallelStream()                       // 声明作为流处理
                   .filter(string -> string.isEmpty())     // 中间操作，过滤空元素
                   .count();                               // 结束操作，计数
```

---

## 中间操作

### 映射 map

`map` 方法用于映射每个元素到对应的结果，其实就是对结果进行转化。

```java
// 获取对应的平方数
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
```

### 过滤 filter

`filter` 方法用于通过设置的条件过滤出元素。

```java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 获取空字符串的数量
long count = strings.stream().filter(string -> string.isEmpty()).count();
```

### 筛选 limit/skip

`limit` 方法用于获取指定数量的流(前 n 个)， `skip` 方法用于去除指定数量的流(前 n 个)。

```java
// 筛选出 11-20 条数据
Random random = new Random();
random.ints().limit(20).skip(10).forEach(System.out::println);
```

### 排序 sorted

`sorted` 方法通过 Comparable 接口对流进行排序，也可以自定义。

```java
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
```

### 去重 distinct

`distinct` 方法通过流元素的 hashCode 和 equals 方法去除重复元素。

```java
Random random = new Random();
random.ints().distinct().forEach(System.out::println);
```

---

## 结束操作

### 迭代 forEach

结束操作： `forEach` 迭代流中的每个数据，即对每个数据进行最后的处理（比如保存到数据库中或打印）。

```java
// 输出 10 个随机数 
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

> 不要用 forEach 方法收集元素！stream 并行执行会损害正确性和效率，使用下方操作。

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
