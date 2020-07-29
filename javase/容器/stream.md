# 流 Stream

---

## 流概念

JDK 1.8 新增。将要处理的元素集合看作一种流，在管道的节点上进行处理。使代码更简洁易读。

```java
List<Integer> transactionsIds = 
widgets.stream()                                            // 声明作为流处理
       .filter(b -> b.getColor() == RED)                    // 过滤
       .sorted((x,y) -> x.getWeight() - y.getWeight())      // 排序
       .mapToInt(Widget::getWeight)                         // 转化
       .sum();                                              // 汇总
```


### 生成流

集合接口有两个方法来生成流：

- `stream` 方法：为集合创建串行流。
- `parallelStream` 方法：为集合创建并行流。

parallelStream 是流并行处理程序的代替方法。

```java
// 获取空字符串的数量
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
int count = strings.parallelStream().filter(string -> string.isEmpty()).count();
```

### 迭代 forEach

Stream 提供了新的方法 `forEach` 来迭代流中的每个数据，即对每个数据进行处理。

```java
// 输出 10 个随机数 
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

### 映射 map

`map` 方法用于映射每个元素到对应的结果。

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

limit 方法用于获取指定数量的流。 

```java
// 筛选出 10 条数据
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

### 排序 sorted

sorted 方法用于对流进行排序。以下代码片段使用 sorted 方法对输出的 10 个随机数进行排序：

```java
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
```


### 聚合 Collectors

Collectors 类实现了很多归约操作，例如将流转换成集合和聚合元素。Collectors 可用于返回列表或字符串：

```java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
 
System.out.println("筛选列表: " + filtered);
String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
System.out.println("合并字符串: " + mergedString);
```


### SummaryStatistics 统计

另外，一些产生统计结果的收集器也非常有用。它们主要用于int、double、long等基本类型上。

```
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
 
IntSummaryStatistics stats = numbers.stream().mapToInt((x) -> x).summaryStatistics();
 
System.out.println("列表中最大的数 : " + stats.getMax());
System.out.println("列表中最小的数 : " + stats.getMin());
System.out.println("所有数之和 : " + stats.getSum());
System.out.println("平均数 : " + stats.getAverage());
```