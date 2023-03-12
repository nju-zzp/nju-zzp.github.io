---
title: "Java8: Lambda与Stream" #标题
date: 2023-03-12T22:25:46+08:00 #创建时间
lastmod: 2023-03-12T22:25:46+08:00 #更新时间
author: ["River"] #作者
keywords: #关键词
    - "java"
    - "lambda"
    - "stream"
categories: #类别
- 
tags: #标签
    - "java"
    - "lambda"
    - "stream"
description: "" #描述
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true #是否展示评论
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "images/stream.png" #图片路径：posts/tech/文章1/picture.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: true
    
reward: true # 打赏
---

# Java8: 函数式数据处理｜Lambda与Stream

## 一、行为参数化传递代码

在编写代码时，我们经常面对一些不断变化的需求：选出不同条件的水果、选用不同的策略做比较排序……

所以我们经常把这样变化的需求做一层抽象，将变化的行为作为参数，传递行为的代码就好。

于是一个代表这个抽象的行为的接口/抽象类就出现了。

但定义抽象类，再定义具体类，并创建对象传递，代码很啰嗦，于是匿名类出现了。

但还不够方便，于是Lambda表达式出现了，简单地理解为，它代替了匿名类来传递行为

## 二、Lambda 表达式

### 2.1 Lambda表达式的结构

匿名函数，没有名称，不属于某个特定的类，但有：

- 参数列表：参数类型、参数名
- 函数体：`{ coding here }`
- 返回类型：返回的值都有类型或为void
- 异常列表：有异常抛出可以有

![img](https://res.weread.qq.com/wrepub/epub_26211813_42)

### 2.2 使用Lambda表达式条件：函数式接口

#### 2.2.1 函数式接口

- 一言以蔽之，函数式接口就是只定义一个抽象方法的接口。（可以有默认方法）
- 而Lambda表达式的函数签名，就是函数式接口里唯一的抽象方法的函数签名
- Lambda表达式允许你直接以内联的形式为函数式接口的抽象方法提供实现，并把整个表达式作为函数式接口的实例。

![image-20230311005126086](https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/image-20230311005126086.png)

#### 2.2.2 Java8中常用的函数式接口

![image-20230311005356680](https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/image-20230311005356680.png)

- `Predicate<T>`
- `Consumer<T>`
- `Supplier<T>`
- `Function<T, R>`
- `UnaryOperator<T>`
- `BinaryOperator<T>`
- `BiPredicate<L,R>`
- `BiConsumer<T,U>`
- `BiFunction<T,U,R>`

#### 2.2.3 Lambda的简化与类型推断

Lambda的**类型**是从**使用Lambda的上下文推断**出来的。

上下文（比如，接受它传递的方法的参数，或接受它的值的局部变量）中Lambda表达式需要的类型称为**目标类型**

<img src="https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/image-20230311010027787.png" alt="image-20230311010027787" style="zoom:67%;" />

Java编译器会从上下文（目标类型）推断出用什么函数式接口来配合Lambda表达式，这意味着它也可以推断出适合Lambda的签名，因为函数描述符可以通过目标类型来得到。

这样做的好处在于，编译器可以了解Lambda表达式的参数类型，这样就**可以在Lambda语法中省去标注参数类型**。

### 2.3 对局部变量的限制

Lambda表达式也允许使用自由变量（不是参数，而是在外层作用域中定义的变量）。但这个变量有所限制。

Lambda可以**没有限制地捕获（也就是在其主体中引用）实例变量和静态变量**。

但**局部变量必须显式声明为final，或事实上是final**。换句话说，Lambda表达式只能捕获指派给它们的局部变量一次。（注：捕获实例变量可以被看作捕获最终局部变量this。）

**对局部变量限制的原因**：

1. 实例变量和局部变量背后的实现有一个关键不同。实例变量都存储在堆中，而局部变量则保存在栈上。如果Lambda可以直接访问局部变量，而且Lambda是在一个线程中使用的，则使用Lambda的线程，可能会在分配该变量的线程将这个变量收回之后，去访问该变量。因此，**Java在访问自由局部变量时，实际上是在访问它的副本，而不是访问原始变量**。如果局部变量仅仅赋值一次那就没有什么区别了——因此就有了这个限制。
2. 不鼓励你使用改变外部变量的典型命令式编程模式，这会给多线程/并行处理带来巨大麻烦

### 2.4 方法引用

方法引用可以被看作仅仅调用特定方法的Lambda的一种快捷写法。

- 基本思想：如果一个Lambda代表的只是“直接调用这个方法”，那最好还是用名称来调用它，而不是去描述如何调用它
- 结构：目标引用放在分隔符`::`前，方法的名称放在后面，即`target_class_or_obj::mthod_name`
- 注意：记住，不需要括号，因为没有实际调用这个方法

方法引用主要有三类：

1. 指向**静态方法**的方法引用（例如 Integer 的`parseInt`方法，写作**`Integer::parseInt`**）。
2. 指向参数对象的**任意类型实例方法**的方法引用（例如 String 的`length`方法，写作**`String::length`**）
3. 指向**现有对象的实例方法**的方法引用（假设你有一个外部局部变量`expensiveTransaction`用于存放 Transaction 类型的对象，它支持实例方法`getValue`，那么你就可以写`expensiveTransaction::getValue`）

第二种和第三种方法引用可能乍看起来有点儿晕。类似于`String::length`的第二种方法引用的思想就是你在引用一个对象的方法，而这个对象本身是Lambda的一个参数。例如，Lambda表达式`(String s)-> s.toUppeCase()`可以写作`String::toUpperCase`。但第三种方法引用指的是，你在Lambda中调用一个已经存在的外部对象中的方法。例如，Lambda表达式`()->expensiveTransaction.getValue()`可以写作`expensiveTransaction::getValue`。

### 2.5 特殊函数式接口的复合

#### 2.5.1 Function接口：andThen & compose

- andThen方法会返回一个函数，它先对输入应用一个给定函数，再对输出应用另一个函数。
- compose方法，先把给定的函数用作compose的参数里面给的那个函数，然后再把函数本身用于结果

## 三、初识Stream

### 3.1 流的概念

定义：从**支持数据处理操作的源**生成的**元素序列**

- 元素序列——就像集合一样，流也提供了一个接口，可以访问特定元素类型的一组有序值。但流的目的在于表达计算，比如你前面见到的filter、sorted和map。**集合讲的是数据，流讲的是计算**。
- 源——流会使用一个提供数据的源，如集合、数组或输入/输出资源。
- 数据处理操作——流的数据处理功能支持类似于数据库的操作，以及函数式编程语言中的常用操作，如filter、map、reduce、find、match、sort等

特点：

- 流水线——很多流操作本身会返回一个流，这样多个操作就可以链接起来，形成一个大的流水线。
- 内部迭代——与使用迭代器显式迭代的集合不同，流的迭代操作是在背后进行的。

流与集合的比较：

- 粗略地说，集合与流之间的差异就在于什么时候进行计算。集合是一个内存中的数据结构，它包含数据结构中目前所有的值——集合中的每个元素都得先算出来才能添加到集合中。相比之下，流则是在概念上固定的数据结构（你不能添加或删除元素），其元素则是**按需计算**的。从另一个角度来说，流就像是一个**延迟创建**的集合：只有在消费者要求的时候才会计算值。与此相反，集合则是急切创建的。
- 流**只能遍历一次**。遍历完之后，我们就说这个流已经被消费掉了。
- 使用Collection接口需要用户去做迭代（比如用for-each），这称为外部迭代。相反，Streams库使用**内部迭代**——它帮你把迭代做了，还把得到的流值存在了某个地方，你只要给出一个函数说要干什么就可以了。

### 3.2 流的操作

流有两类操作：

- 中间操作：返回另一个流的操作。这让多个操作可以连接起来形成一个查询。
  - 注意：除非流水线上触发一个终端操作，否则中间操作不会执行任何处理——它们~~很懒的~~延迟执行
  - filter
  - map
  - limit
  - sorted
  - distinct
- 终端操作：从流的流水线生成结果，其结果是任何不是流的值，比如List、Integer，甚至void。
  - 终端操作触发整个流水线的构建执行
  - foreach
  - count
  - collect

使用流的流程：

1. 一个数据源（如集合、文件）转化为流
2. 一个中间操作链，形成一条流的流水线
3. 一个终端操作，执行流水线，并能生成结果

## 四、使用流

### 4.1 筛选和切片

- 用谓词`Predicate<T>`筛选：`filter`
- 筛选出各异的元素：`distinct`
- 截短流：`limit`
- 跳过元素：`skip`

### 4.2 映射

- 对流中每个元素应用`Function<T, U>`，生成新的流：`map`

- 流的扁平化：`flatmap`

  - `flatmap`方法让你把一个流中的每个值都换成另一个流，然后把所有的流连接起来成为一个流

    ![img](https://res.weread.qq.com/wrepub/epub_26211813_112)

    ```java
              List<Integer> numbers1=Arrays.asList(1, 2, 3);
              List<Integer> numbers2=Arrays.asList(3, 4);
              List<int[]> pairs=
                  numbers1.stream()
                          .flatMap(i-> numbers2.stream()
                                                  .map(j-> new int[]{i, j})
                                  )
                          .collect(toList());
    ```

### 4.3 查找和匹配

另一个常见的数据处理套路是看看数据集中的某些元素是否匹配一个给定的属性。Stream API通过`allMatch`、`anyMatch`、`noneMatch`、`findFirst`和`findAny`方法提供了这样的工具。

匹配Match返回的是`Boolean`，**查找Find返回的是`Optional<T>`**（因为可能什么元素都没找到，引入这个类代表一个值存在或不存在，详见4.4）

- 检查谓词是否至少匹配一个元素：`anyMatch`
- 检查谓词是否匹配所有元素：`allMatch`
- 查找当前流中的任意元素：`findAny`
- 查找第一个元素：`findFirst`
- 因此查找经常用在filter后

### 4.4 Optional\<T> 类

Optional\<T>类（`java.util.Optional`）是一个容器类，代表一个值存在或不存在。

- `isPresent()`将在Optional包含值的时候返回true，否则返回false。
- `ifPresent(Consumer<T> block)`会在值存在的时候执行给定的代码块。我们2.2介绍了Consumer函数式接口；它让你传递一个接收T类型参数，并返回void的Lambda表达式
- `T get()`会在值存在时返回值，否则抛出一个NoSuchElement异常。
- **`T orElse(T other)`会在值存在时返回值，否则返回一个默认值**（用的最多）
- **`T orElseGet(Supplier<? extends T> other)` 如果存在值，则返回该值，否则调用Supplier并返回该调用的结果**

### 4.5 规约 reduce

- 双参数版本 `T reduce(T identity, BinaryOperator<T> accumulator)`

  - 一个初始值，作为累积值的初始值，与流的第一个元素执行第二个方法
  - 一个`BinaryOperator<T>`，将当前累积值与流中的元素计算，获得新的值作为下次累积值

- 单参数版本：`Optional<T> reduce(BinaryOperator<T> accumulator)`

  - 它不接受初始值，但是会返回一个Optional对象。因为没有初始值，所以结果不一定存在。

- 例子：计算最大值/最小值

  ```java
  Optional<Integer> max=numbers.stream().reduce(Integer::max);

  Optional<Integer> min=numbers.stream().reduce(Integer::min);
  ```

### 4.6 数值流：原始类型特化流

Java 8引入了三个原始类型特化流接口：`IntStream`、`DoubleStream`和`LongStream`，分别将流中的元素特化为int、long和double，从而**避免了暗含的装箱成本**。每个接口都带来了进行**常用数值归约的新方法**，比如对**数值流求和的sum**，**找到最大元素的max**。此外还有在必要时再把它们转换回对象流的方法。

转化为数值流：

- 将流转换为特化版本的常用方法是`mapToInt`、`mapToDouble`和`mapToLong`。这些方法和前面说的map方法的工作方式一样，只是它们返回的是一个特化流，而不是`Stream<T>`。

转化回对象流：

- 要把原始流转换成一般流（每个int都会装箱成一个Integer），可以使用boxed方法。

数值流——从数值范围生成流

- 和数字打交道时，有一个常用的东西就是数值范围。比如，假设你想要生成1和100之间的所有数字。Java 8引入了两个可以用于`IntStream`和`LongStream`的静态方法，帮助生成这种范围：`range`和`rangeClosed`。
- 这两个方法都是第一个参数接受起始值，第二个参数接受结束值。
- 但`range`是不包含结束值的，而`rangeClosed`则包含结束值。

## 五、构建流

### 5.1 由值创建

你可以使用静态方法**`Stream.of`**，通过显式值创建一个流。它可以接**受任意数量的参数**。

Stream类：`static <T> Stream<T>	of(T... values)`：Returns a sequential ordered stream whose elements are the specified values.

### 5.2 由数组创建

你可以使用静态方法**`Arrays.stream`**从**数组创建一个流**。它接受一个数组作为参数。

Arrays类：`static <T> Stream<T>	stream(T[] array)`：返回顺序[`Stream`](https://www.matools.com/file/manual/jdk_api_1.8_google/java/util/stream/Stream.html)与指定的数组作为源

原始类型会转化为数值流（原始类型特化流）

### 5.3 由集合创建

Collection接口有默认方法`default Stream<E> stream()`：直接`collectionObj.stream()`

### 5.4 由文件生成

java中用于处理文件等I/O操作的`NIO` API（非阻塞I/O）已更新，以便利用Stream API。`java.nio.file.Files`中的很多静态方法都会返回一个流。例如，一个很有用的方法是`Files.lines`，它会返回一个由指定文件中的各行构成的字符串流。

[`java.nio.file` package 官方文档](https://docs.oracle.com/javase/8/docs/api/java/nio/file/package-summary.html)

```
public static Stream<String> lines(Path path)
```

### 5.5 由函数生成

Stream API提供了两个静态方法来从函数生成流：`Stream.iterate` 和 `Stream.generate`。这两个操作可以创建所谓的无限流：不像从固定集合创建的流那样有固定大小的流。由`iterate`和`generate`产生的流会用给定的函数按需创建值，因此可以无穷无尽地计算下去！一般来说，应该使用`limit(n)`来对这种流加以限制

#### 5.5.1 Stream.iterate

```java
static <T> Stream<T> iterate(T seed,
                             UnaryOperator<T> f)
```

Returns an infinite sequential ordered Stream produced by iterative application of a function f to an initial element seed, producing a Stream consisting of seed, f(seed), f(f(seed)), etc.

The first element (position 0) in the Stream will be the provided seed. For n > 0, the element at position n, will be the result of applying the function f to the element at position n - 1.

**Type Parameters:**

`T` - the type of stream elements

**Parameters:**

`seed` - the initial element

`f` - a function to be applied to the previous element to produce a new element

**Returns:**

a new sequential `Stream`

#### 5.5.2 Stream.generate

```
static <T> Stream<T> generate(Supplier<T> s)
```

Returns an infinite sequential unordered stream where each element is generated by the provided `Supplier`. This is suitable for generating constant streams, streams of random elements, etc.

- **Type Parameters:**

  `T` - the type of stream elements

- **Parameters:**

  `s` - the `Supplier` of generated elements

- **Returns:**

  a new infinite sequential unordered `Stream`

## 六、Collector收集数据

### 6.1 Collector的功能

`Collector`接口是传递给stream的`collect( xxx )`方法的参数。

`Collectors`类是关于`Collector`的工具类。

我们一般使用预定义收集器——`Collectors`类提供的工厂方法（例如groupingBy）创建的收集器。

它们主要提供了三大功能：

- 将元素规约和汇总成一个值：比如求和、平均、最大、最小等等
- 元素分组：根据分组函数结果作为分组条件，返回`Map<T, List<U>>`
- 元素分区：分组的特殊情况，使用谓词（返回一个布尔值的单参数函数）作为分组函数，返回`Map<Boolean, List<T>>`

### 6.2 规约汇总成一个值

- 计数：`Collectors.counting()`

- 求最大最小：`Collectors.maxBy(Comparator_implement_obj)`

  - 参数为一个Comparator，可以自定义lambda表达式，更可以使用Comparator接口的静态工厂方法生成
  - 返回结果为一个`Optional<T>`

- 求和：`Collectors.summingInt(xxx)`

- 求平均：`Collectors.averagingInt(xxx)`

- 以上数据均可通过`IntSummaryStatics`获得：`Collectors.summarizingInt(xxx)`

- 字符串连接：`Collectors.joining()`

  - 可选参数带分界符：`Collectors.joining(",")`

- 广义的规约汇总：`Collectors.reducing(初始值，转换函数，累积函数)`

  - 从逻辑上说，归约操作的工作原理为：利用累积函数，把一个初始化为起始值的累加器，和把转换函数应用到流中每个元素上得到的结果不断迭代合并起来。

    ```java
    public static <T> Collector<T, ? , Long> counting() {
    	return reducing(0L, e-> 1L, Long::sum);
    }
    ```

  - 有多种方法的时候，**尽可能使用最专门化的一个，而不是reducing**

### 6.3 分组

类似于数据库，根据一个或多个属性对集合中的项目进行分组，用`Collectors.groupingBy`工厂方法返回的收集器就可以轻松地完成这项任务。例子如下：

```java
Map<Dish.Type, List<Dish>> dishesByType =
    	menu.stream().collect(Collectors.groupingBy(Dish::getType));
```

`groupingBy`最重要的参数就是分类函数Function<T,U>，应用该函数到每个元素，其返回结果就作为分组的key。主要有三种重载：

```java
public static <T,K> Collector<T,?,Map<K,List<T>>> groupingBy(Function<? super T,? extends K> classifier)
// 直接通过分类函数获得的key进行分组，返回Map<K, List<T>>

public static <T,K,A,D> Collector<T,?,Map<K,D>> groupingBy(Function<? super T,? extends K> classifier, Collector<? super T,A,D> downstream)
// 通过分类函数获得的key分组后，再对每个分组里的元素应用一个Collector（可应用于求每组的一个值，或多重分组）

public static <T,K,D,A,M extends Map<K,D>> Collector<T,?,M> groupingBy(Function<? super T,? extends K> classifier, Supplier<M> mapFactory, Collector<? super T,A,D> downstream)
// 通过Map的工厂生成函数Supplier<M>规定了生成的Map的具体Map类，比如传递TreeMap::new获取能将Key排序的Map
```

扩展：多级分组如何做？

- 要进行二级分组的话，我们可以把一个内层`groupingBy`传递给外层groupingBy的第二个参数，内层groupingBy就定义了为第一个分组里再次分类分组的二级标准

### 6.4 把收集后的结果进行转换

把收集器返回的结果转换为另一种类型，你可以使用`Collectors.collectingAndThen`工厂方法返回的收集器，如下所示

```java
public static <T,A,R,RR> Collector<T,A,RR> collectingAndThen(Collector<T,A,R> downstream, Function<R,RR> finisher)
```

### 6.5 Collectors生成的预定义Collector汇总

![img](https://res.weread.qq.com/wrepub/epub_26211813_148)

![img](https://res.weread.qq.com/wrepub/epub_26211813_149)

