---
title: Stream流式编程
tags:
  - java
  - Stream
describe: 内容参考CodeSheep
categories: Stream
cover: 'https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/images/cover9.jpg'
abbrlink: 4489
date: 2022-04-15 19:27:02
---

## 概要

stream是用于对迭代器的增强，使之完成更高效的聚合操作（过滤，排序，统计分组）或者大批量数据操作。

stream和lambada表示结合后会大大提升编程效率。

通过将集合转换为这么一种叫做 “流” 的元素序列，通过声明性方式，能够对集合中的每个元素进行一系列并行或串行的流水线操作。

比如过滤保留部分元素、对元素进行排序、类型转换等；最后再进行一个终端操作，可以把 Stream 转换回集合类型，也可以直接对其中的各个元素进行处理，比如打印、比如计算总数、计算最大值等等。

![stream](https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0001.png)

## 一分类

Stream提供方法很多，按照调用当前方法是否结束流处理，可以分为中间操作和结束操作。

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0002.png" alt="stream" style="zoom:50%;" />

### 1中间操作

- 无状态操作：指当前元素的操作不受前面元素的影响，前后数据是否有依赖关系。
- 有状态操作：指当前元素的操作需要等所有元素处理完之后才能进行。

比如说：sort(),distinct()需要知道先前的数才能操作，属于有状态操作



### 2结束操作



- 短路操作:指不需要处理完全部的元素就可以结束。
- 非短路操作: 指必须处理完所有元素才能结束。

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0003.png" alt="stream" style="zoom: 33%;" />



### 3Stream流的三种创建方式





```java
//1集合创建stream流是          集合名.stream();
List<Integer> list = Arrays.asList(1,2,3);
Stream<Integer> stream = list.stream();
//2数组创建stream流是           Arrays.stream(数组名)
IntStream stream = Arrays.stream(new int[]{3, 2, 1});


//3Stream 静态方法
Stream<Integer> stream = Stream.of(1, 2, 3);
Stream<String> stream0 = Stream.of("ab", "abc", "abcd", "abcde", "abcdef");

Stream<Integer> stream2 = Stream.iterate(0, (x) -> x + 3).limit(5);
stream2.forEach(r -> System.out.print(r + " "));

System.out.println();

Stream<Integer> stream3 = Stream.generate(new Random()::nextInt).limit(3);
stream3.forEach(r -> System.out.print(r + " "));
```





## 二无状态操作

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0005.png" alt="stream" style="zoom: 33%;" />

### 2.1. map

接收一个函数作为入参，把这个函数应用到每个元素上，执行结果组成一个新的 stream 返回。

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0004.png" alt="stream" style="zoom:50%;" />

例子1：对整数数组每个元素加 3 ：

```java
List<Integer> list=Array.asList(5,2,3,1,4);
list.stream().map(v->v+3).collect(Collectors.toList);
```

结果为：

> 8 5 6 4 7

例子2: 把字符数组每个元素都变成大写

```java
public static void main(String[] args) {
    List<String> list = Arrays.asList("abs", "sds", "hello");
    List<String> collect = list.stream().map(s -> s.toUpperCase(Locale.ROOT)).collect(Collectors.toList());
    System.out.println(collect);
}
```

结果为

> [ABS, SDS, HELLO]





### 2.2 mapToXXX

包括三个方法：mapToInt、mapToDouble、mapToLong

案例 3：把字符串数组转为整数数组：

```java
List<String> list = Arrays.asList("ab", "abc", "abcd", "abcde", "abcdef");
int[] newList = list.stream().mapToInt(r -> r.length()).toArray();
System.out.println("newList:" + Arrays.toString(newList));
```



结果：

> newList:[2, 3, 4, 5, 6]





### 2.3 flatMap

flatMap接收函数作为入参，然后把集合中每个元素转换成一个 stream，再把这些 stream 组成一个新的 stream，是拆分单词很好的工具。如下图：

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0008.png" alt="stream" style="zoom:50%;" />

案例 4：把一个字符串数组转成另一个字符串数组：

```java
List<String> list = Arrays.asList("ab-abc-abcd-abcde-abcdef", "5-2-3-1-4");
List<String> newList = list.stream().flatMap(s -> Arrays.stream(s.split("-"))).collect(Collectors.toList());
System.out.println("newList：" + newList);
```



上面代码输出结果：

> newList：[ab, abc, abcd, abcde, abcdef, 5, 2, 3, 1, 4]





### 2.4 flatMapToXXX



类似于 flatMap，返回一个 XXXStream。

包括三个方法：flatMapToInt、flatMapToLong、flatMapToDouble

案例 5：对给定的二维整数数组求和:

```java
 int[][] data = {{1,2},{3,4},{5,6}};
 IntStream intStream = Arrays.stream(data).flatMapToInt(a -> Arrays.stream(a));
 int[] ints = intStream.map(a -> a + 1).toArray();
 System.out.println(Arrays.toString(ints));
```

输出：

> [2, 3, 4, 5, 6, 7]





```java
int[][] data = {{1,2},{3,4},{5,6}};
IntStream intStream = Arrays.stream(data).flatMapToInt(row -> Arrays.stream(row));
System.out.println(intStream.sum());
```

​	输出

> 21





### 2.5 filter



筛选功能，按照一定的规则将符合条件的元素提取到新的流中。

定义一个学生类，包含姓名、年龄、性别、考试成绩四个属性



```java
class Student{
    private String name;
    private Integer age;
    private String sex;
    private Integer score;

    public Student(String name, Integer age, String sex, Integer score){
        this.name = name;
        this.age = age;
        this.score = score;
        this.sex = sex;
    }
    //省略getters/setters
}
```



案例 6：找出考试成绩在 90 分以上的学生姓名：

```java
List<Student> students = new ArrayList<>();
students.add(new Student("Mike", 10, "male", 88));
students.add(new Student("Jack", 13,"male", 90));
students.add(new Student("Lucy", 15,"female", 100));
students.add(new Student("Jessie", 12,"female", 78));
students.add(new Student("Allon", 16,"female", 92));
students.add(new Student("Alis", 22,"female", 50));
List<String>list=students.stream().filter(a->a.get(score)>90).map(a->a.getName()).collect(Collectors.toList());

 System.out.println(list);
```

结果

> [Jack, Lucy, Allon]



### 2.6 peek



返回由 stream 中元素组成的新 stream，用给定的函数作用在新 stream 的每个元素上。传入的函数是一个 Consume  类型的，没有返回值，因此并不会改变原 stream 中元素的值。peek 主要用是 debug，可以方便地 查看流处理结果是否正确。

案例 7：过滤出 stream 中长度大于 3 的字符串并转为大写：

```java
Stream.of("one", "two", "three", "four")
             .filter(e -> e.length() > 3)
             .peek(e -> System.out.println("Filtered value: " + e))
             .map(String::toUpperCase)
             .peek(e -> System.out.println("Mapped value: " + e))
             .collect(Collectors.toList());
```



输出结果如下：

> Filtered value: three 
>
> Mapped value: THREE 
>
> Filtered value: four 
>
> Mapped value: FOUR





### 2.7 unordered

把一个有序的 stream 转成一个无序 stream ，如果原 stream 本身就是无序的，可能会返回原始的 stream。

案例 8：把一个有序数组转成无序



```java
Arrays.asList("1", "2", "3", "4", "5")
                .parallelStream()//并行编程。只有并行编程时才无序
                .unordered()
                .forEach(r -> System.out.print(r + " "));
```



每次执行输出的结果不一样，下面是一次输出的结果：

> 3 5 4 2 1





## 三 有状态操作



<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0006.png" alt="stream" style="zoom:50%;" />

### 3.1 distinct

去重功能。

案例 9 ：去掉字符串数组中的重复字符串

```java
String[] array = { "a", "b", "b", "c", "c", "d", "d", "e", "e"};
List<String> newList = Arrays.stream(array).distinct().collect(Collectors.toList());
System.out.println("newList:" + newList);
```

输出结果：

> newList:[a, b, c, d, e]





### 3.2 limit



限制从 stream 中获取前 n 个元素。

案例 10 ：从数组中获取前 5 个元素

```java
String[] array = { "c", "c", "a", "b", "b", "e", "e", "d", "d"};
List<String> newList = Arrays.stream(array).limit(5).collect(Collectors.toList());
System.out.println("newList:" + newList);
```

输出结果：

> newList:[c, c, a, b, b]





### 3.3 skip

跳过 Stream 中前 n 个元素

案例 11：从数组中获取第 5 个元素之后的元素

```java
String[] array = { "a", "b", "c", "d", "e", "f", "g", "h", "i"};
List<String> newList = Arrays.stream(array).skip(5).collect(Collectors.toList());
System.out.println("newList:" + newList);
```

输出结果：

> newList:[f, g, h, i]





### 3.4 sorted



排序功能。

案例 12：对给定数组进行排序



```java
String[] array = { "c", "c", "a", "b", "b", "e", "e", "d", "d"};
List<String> newList = Arrays.stream(array).sorted().collect(Collectors.toList());
System.out.println("newList:" + newList);
```



输出结果：

> newList:[a, b, b, c, c, d, d, e, e]



案例 13：按照学生成绩进行排序



```java
List<Student> students = new ArrayList<>();
students.add(new Student("Mike", 10, "male", 88));
students.add(new Student("Jack", 13,"male", 90));
students.add(new Student("Lucy", 15,"female", 100));
students.add(new Student("Jessie", 12,"female", 78));
students.add(new Student("Allon", 16,"female", 92));
students.add(new Student("Alis", 22,"female", 50));

List<String> nameList = students.stream().sorted(Comparator.comparing(Student::getScore)).map(Student::getName).collect(Collectors.toList());
System.out.print("按成绩排序输出学生姓名：" + nameList);
```



> 考试成绩90分以上的学生姓名：[Alis, Jessie, Mike, Jack, Allon, Lucy]





## 四短路操作

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0007.png" alt="stream" style="zoom:50%;" />

### 4.1 findAny

找出 stream 中任何一个满足过滤条件的元素。

案例 14：找出任何一个成绩高于 90 分的学生

```java
List<Student> students = new ArrayList<>();
students.add(new Student("Mike", 10, "male", 88));
students.add(new Student("Jack", 13,"male", 90));
students.add(new Student("Lucy", 15,"female", 100));
students.add(new Student("Jessie", 12,"female", 78));
students.add(new Student("Allon", 16,"female", 92));
students.add(new Student("Alis", 22,"female", 50));

Optional<Student> studentFindAny = students.stream().filter(x -> x.getScore() > 90).findAny();
System.out.print("找出任意一个考试成绩在90分以上的学生姓名：" + studentFindAny.orElseGet(null).getName());
```

> 找出任意一个考试成绩在90分以上的学生姓名：Lucy





### 4.2 anyMatch

是否存在任意一个满足给定条件的元素。

案例 15：是否存在成绩高于 90 分的学生，是否存在成绩低于 50 分的学生。还是采用上面案例 14 中的学生集合。

```java
boolean result1 = students.stream().allMatch(x -> x.getScore() > 90);
System.out.println("是否所有学生的成绩都高于90分：" + result1);
boolean result2 = students.stream().allMatch(x -> x.getScore() > 50);
System.out.print("是否所有学生的成绩都高于50分：" + result2);
```



输出结果：

> 是否所有学生的成绩都高于90分：false
>
> 是否所有学生的成绩都高于50分：true





### 4.3 allMatch

是否集合中所有元素都满足给定的条件，如果集合是空，则返回 true。

案例 16：学生成绩是否都高于 90 分，是否都高于 50 分。还是采用上面案例 14 中的学生集合。



```java
boolean result1 = students.stream().allMatch(x -> x.getScore() > 90);
System.out.println("是否所有学生的成绩都高于90分：" + result1);
boolean result2 = students.stream().allMatch(x -> x.getScore() > 50);
System.out.print("是否所有学生的成绩都高于50分：" + result2);
```



输出结果：

> 是否所有学生的成绩都高于90分：false
>
> 是否所有学生的成绩都高于50分：true





### 4.4 noneMatch

是否没有元素能匹配给定的条件，如果集合是空，则返回 true。

案例 17：是不是没有学生成绩在 90 分以上，是否没有学生成绩在 50 分以下。还是采用上面案例 14 中的学生集合。

```java
boolean result1 = students.stream().noneMatch(x -> x.getScore() > 90);
System.out.println("是不是没有学生成绩在 90 分以上：" + result1);
boolean result2 = students.stream().noneMatch(x -> x.getScore() < 50);
System.out.print("是不是没有学生成绩在 50 分以下：" + result2);
```



输出结果：

> 是不是没有学生成绩在 90 分以上：false
>
> 是不是没有学生成绩在 50 分以下：true







### 4.5 findFirst

找出第一个符合条件的元素。

案例 18：找出第一个成绩在 90 分以上的学生。还是采用上面案例 14 中的学生集合。



```java
Optional<Student> studentFindAny = students.stream().filter(x -> x.getScore() > 90).findFirst();
System.out.print("第一个成绩在 90 分以上的学生姓名：" + studentFindAny.orElseGet(null).getName());

输出结果：
```



输出结果：

> 第一个成绩在 90 分以上的学生姓名：Lucy







## 五非短路操作

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0009.png" alt="stream" style="zoom:50%;" />

### 5.1 forEach

遍历元素。

案例 19：遍历一个数组并打印

```java
List<Integer> array = Arrays.asList(5, 2, 3, 1, 4);
array.stream().forEach(System.out :: println);
```

输出结果：

> 5 2 3 1 4







### 5.2 forEachOrdered



按照给定集合中元素的顺序输出。主要使用场景是在并行流的情况下，按照给定的顺序输出元素。

案例 20：用并行流遍历一个数组并按照给定数组的顺序输出结果



```java
List<Integer> array = Arrays.asList(5, 2, 3, 1, 4);
array.parallelStream().forEachOrdered(System.out :: println);
```

输出结果：

> 5 2 3 1 4





### 5.3 toArray

返回包括给定 stream 中所有元素的数组。

案例 21：把给定字符串流转化成数组



```java
Stream<String> stream = Arrays.asList("ab", "abc", "abcd", "abcde", "abcdef").stream();
String[] newArray1 = stream.toArray(str -> new String[5]);
String[] newArray2 = stream.toArray(String[]::new);
Object[] newArray3 = stream.toArray();
```







### 5.4 reduce

规约操作，把一个流的所有元素合并成一个元素，比如求和、求乘积、求最大最小值等。

案例 22：求整数数组元素之和、乘积和最大值

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
Optional<Integer> sum = list.stream().reduce((x, y) -> x + y);
Optional<Integer> product = list.stream().reduce((x, y) -> x * y);
Optional<Integer> max = list.stream().reduce((x, y) -> x > y ? x : y);
System.out.println("数组元素之和：" + sum.get());
System.out.println("数组元素乘积：" + product.get());
System.out.println("数组元素最大值：" + max.get());
```



输出结果：

> 数组元素之和：15
>
> 数组元素乘积：120 
>
> 数组元素最大值：5

案例 23：求全班学生最高分、全班学生总分

```java
List<Student> students = new ArrayList<>();
students.add(new Student("Mike", 10, "male", 88));
students.add(new Student("Jack", 13,"male", 90));
students.add(new Student("Lucy", 15,"female", 100));
students.add(new Student("Jessie", 12,"female", 78));
students.add(new Student("Allon", 16,"female", 92));
students.add(new Student("Alis", 22,"female", 50));
Optional<Integer> maxScore = students.stream().map(r -> r.getScore()).reduce(Integer::max);
Optional<Integer> sumScore = students.stream().map(r -> r.getScore()).reduce(Integer::sum);
System.out.println("全班学生最高分：" + maxScore.get());
System.out.println("全班学生总分：" + sumScore.get());
```

输出结果：

> 全班学生最高分：100 
>
> 全班学生总分：498





### 5.5 collect

把 stream 中的元素归集到新的集合或者归集成单个元素。



#### 5.5.1 归集成新集合

方法包括 toList、toSet、toMap。

案例 24：根据学生列表，归纳出姓名列表、不同分数列表、姓名分数集合，其中 Mike 和 Jessie 的分数都是 88。

```java
List<Student> students = new ArrayList<>();
students.add(new Student("Mike", 10, "male", 88));
students.add(new Student("Jack", 13,"male", 90));
students.add(new Student("Lucy", 15,"female", 100));
students.add(new Student("Jessie", 12,"female", 88));
students.add(new Student("Allon", 16,"female", 92));
students.add(new Student("Alis", 22,"female", 50));
List<String> list = students.stream().map(r -> r.getName()).collect(Collectors.toList());
Set<Integer> set = students.stream().map(r -> r.getScore()).collect(Collectors.toSet());
Map<String, Integer> map = students.stream().collect(Collectors.toMap(Student::getName, Student::getScore));
System.out.println("全班学生姓名列表：" + list);
System.out.println("全班学生不同分数列表：" + set);
System.out.println("全班学生姓名分数集合：" + map);
```

输出结果：

> 全班学生姓名列表：[Mike, Jack, Lucy, Jessie, Allon, Alis] 
>
> 全班学生不同分数列表：[50, 100, 88, 90, 92] 
>
> 全班学生姓名分数集合：{Mike=88, Allon=92, Alis=50, Lucy=100, Jack=90, Jessie=88}



#### 5.5.2 统计功能

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0010.png" alt="stream" style="zoom:50%;" />

统计功能包括如下方法：

<img src="https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/stream/stream/0011.png" alt="stream" style="zoom:50%;" />



   案例 25：求总数、求和、最大/最小/平均值

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
long count = list.stream().collect(Collectors.counting());
int sum = list.stream().collect(Collectors.summingInt(r -> r));
double average = list.stream().collect(Collectors.averagingDouble(r -> r));
Optional<Integer> max = list.stream().collect(Collectors.maxBy(Integer::compare));
Optional<Integer> min = list.stream().collect(Collectors.maxBy((x, y) -> x > y ? y : x));
System.out.println("总数:" + count);
System.out.println("总和:" + sum);
System.out.println("平均值:" + average);
System.out.println("最大值:" + max.get());
System.out.println("最小值:" + min.get());
```



输出结果：

> 总数:5 
>
> 总和:15 
>
> 平均值:3.0 
>
> 最大值:5 
>
> 最小值:5

案例 26：求总和统计



```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
IntSummaryStatistics statistics = list.stream().collect(Collectors.summarizingInt(r -> r));
System.out.println("综合统计结果：" + statistics.toString());
```



输出结果：

> 综合统计结果：IntSummaryStatistics{count=5, sum=15, min=1, average=3.000000, max=5}



#### 5.5.3 分区和分组

主要包括两个函数：

- partitioningBy：把 stream 分成两个 map
- groupingBy：把 stream 分成多个 map

案例 27：将学生按照 80 分以上和以下分区

```java
List<Student> students = new ArrayList<>();
students.add(new Student("Mike", 10, "male", 88));
students.add(new Student("Jack", 10,"male", 90));
students.add(new Student("Lucy", 12,"female", 100));
students.add(new Student("Jessie", 12,"female", 78));
students.add(new Student("Allon", 16,"female", 92));
students.add(new Student("Alis", 16,"female", 50));
Map<Boolean, List<Student>> partitionByScore = students.stream().collect(Collectors.partitioningBy(x -> x.getScore() > 80));
System.out.println("将学生按照考试成绩80分以上分区：");
partitionByScore.forEach((k,v ) -> {
    System.out.print(k ? "80分以上：" : "80分以下：");
    v.forEach(r -> System.out.print(r.getName() + ","));
    System.out.println();
});
System.out.println();
```



分区结果是把 Student 列表分成 key 只有 true 和 false 两个值的 map，输出如下：

> 将学生按照考试成绩80分以上分区：
>
> 80分以下：Jessie,Alis,
>
> 80分以上：Mike,Jack,Lucy,Allon,

案例 28：将学生按照性别、年龄分组



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



输出如下：

> 将学生按照性别、年龄分组：
>
> female：
>
>    16:Allon,Alis, 
>
>    12:Lucy,Jessie, 
>
> male：
>
>    10:Mike,Jack,



#### 5.5.4 连接

将 stream 中的元素用指定的连接符合并，连接符可以是空。

案例 29：输出所有学生的姓名，用逗号分隔，这里还是使用案例 27 中的学生列表

```java
String studentNames = students.stream().map(r -> r.getName()).collect(Collectors.joining(","));
System.out.println("所有学生姓名列表：" + studentNames);
```

输出如下：

> 所有学生姓名列表：Mike,Jack,Lucy,Jessie,Allon,Alis





#### 5.5.5 规约

在 5.4 节已经讲过规约了，这里的规约支持更强大的自定义规约。

案例 30：数组中每个元素加 1 后求总和

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
int listSum = list.stream().collect(Collectors.reducing(0, x -> x + 1, (sum, b) -> sum + b));
System.out.println("数组中每个元素加 1 后总和：" + listSum);
```

输出结果：

> 数组中每个元素加 1 后总和：20





### 5.6 max、min、count

stream 提供的方便统计的方法。

案例 31：统计整数数组中最大值、最小值、大于 3 的元素个数

```java
List<Integer> list = Arrays.asList(5, 2, 3, 1, 4);
System.out.println("数组元素最大值："+list.stream().max(Integer::compareTo).get());
System.out.println("数组元素最小值："+list.stream().min(Integer::compareTo).get());
System.out.println("数组中大于3的元素个数："+list.stream().filter(x -> x > 3).count());
```

输出结果：

> 数组元素最大值：5
>
> 数组元素最小值：1
>
> 数组中大于3的元素个数：2

案例 32：统计分数最高的学生姓名

```java
List<Student> students = new ArrayList<>();
students.add(new Student("Mike", 10, "male", 88));
students.add(new Student("Jack", 10,"male", 90));
students.add(new Student("Lucy", 12,"female", 100));
students.add(new Student("Jessie", 12,"female", 78));
students.add(new Student("Allon", 16,"female", 92));
students.add(new Student("Alis", 16,"female", 50));
Optional<Student> optional = students.stream().max(Comparator.comparing(r -> r.getScore()));
System.out.println("成绩最高的学生姓名：" + optional.get().getName());
```

输出结果：

> 成绩最高的学生姓名：Lucy



