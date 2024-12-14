# java 8 补充 by 尚硅谷

## lambda表达式

> ->: Lambda操作符或箭头操作符
> ->的左边：Lambda,形参列表（其实就是接口中的抽象方法的形参列表）
> ->的右边：Lambda体（其实就是重写的抽象方法的方法体）
>
> 作为函数式接口的实例

```java
package com.joriri;

import org.junit.Test;

import java.util.function.Consumer;

/**
 * @Author zxy
 * @Date 2024/12/12
 * @Description lambda表达式
 */
public class lambdaDemo {
    public static void main(String[] args) {
        Consumer<String> con = new Consumer<>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        con.accept("hello");

        System.out.println("--------------");
        Consumer<String> con1 = s -> System.out.println(s);
        con1.accept("world");

    }
}

```

**六种情况**

1. 无参, 无返回值
   ```java
   Runnable r2 = () -> {
               System.out.println("this is lambda expression");
           };
   ```
   
2. 有一个参数, 没有返回值
   ```java
    Consumer<String> con1 = (String s) -> {System.out.println(s);};
   ```

3. 数据类型可以省略，因为可由编译器推断得出，称为“类型推断”

   ```java
    Consumer<String> con1 = (s) -> {System.out.println(s);};
   ```

4. 一个参数时, 小括号可省略
   ```java
    Consumer<String> con1 = s -> {System.out.println(s);};
   ```

5. 需要两个或以上的参数，多条执行语句，并且可以有返回值
   ```java
   Comparator<Integer> integerComparator1 = (x, y) -> {
               System.out.println("let's compare");
               return Integer.compare(x, y);
           };
   ```

6. 当Lambda体只有1条语句时，return与大括号若有，都可以省略
   ```java
   Comparator<Integer> integerComparator2 = (x, y) -> Integer.compare(x,y);
   ```

   

## 函数式接口

如果一个接口中，只声明了一个抽象方法，则此接口就称为函数式接口.

可以使用@FunctionalInterface这个注解

**java内置四大核心函数式接口**

| 函数式接口               | 参数类型 | 返回类型 | 用途                                                         |
| ------------------------ | -------- | -------- | ------------------------------------------------------------ |
| Consumer\<T>消费型接口   | T        | void     | 对类型为T的对象应用操作，包含方法： void accept(Tt)          |
| Supplier\<T>供给型接口   | 无       | T        | 返回类型为T的对象，包含方法：T get()                         |
| Function\<T,R>函数型接口 | T        | R        | 对类型为T的对象应用操作，并返回结果。结 果是R类型的对象。包含方法：R apply(T t) |
| Predicate\<T>断定型接口  | T        | boolean  | 确定类型为T的对象是否满足某约束，并返回 boolean 值。包含方法：boolean test(T t) |

**其他接口**

| 函数式接口                                                   | 参数类型        | 返回类型        | 用途                                                         |
| ------------------------------------------------------------ | --------------- | --------------- | ------------------------------------------------------------ |
| BiFunction\<T,U,R>                                           | T, U            | R               | 对类型为 T,U 参数应用操作，返回 R 类型的结 果。包含方法为：Rapply(Tt,Uu); |
| UnaryOperator \<T>（Function子接口）                         | T               | T               | 对类型为T的对象进行一元运算，并返回T类型的 结果。包含方法为：Tapply(Tt); |
| BinaryOperator\<T> （BiFunction子接口）                      | T, T            | T               | 对类型为T的对象进行二元运算，并返回T类型的 结果。包含方法为：Tapply(Tt1,Tt2); |
| BiConsumer\<T,U>                                             | T, U            | void            | 对类型为T,U 参数应用操作。 包含方法为： void accept(T t, U u) |
| BiPredicate\<T,U>                                            | T,U             | boolean         | 包含方法为： boolean test(T t,U u)                           |
| TolntFunction\<T><br />ToLongFunction\<T><br />ToDoubleFunction\<T> | T               | int long double | 分别计算int、long、double值的函数                            |
| IntFunction\<R><br /> LongFunction\<R><br /> DoubleFunction\<R> | int long double | R               | 参数分别为int、long、double 类型的函数                       |



## 方法引用

使用情景: 当lambda体的操作已经有实现方法了, 就可以用方法引用

要求: 接口的抽象方法的形参列表和返回值类型 与 方法引用的方法的形参列表和返回值类型 相同 (针对前两种情况)

1. 对象::非静态方法
   ```java
     /*
       Consumer的 void accept(T t)
       和
       PrintSteam的 void println(T t)
       形参列表和返回类型是一致的
       所以可以 方法引用
        */
       @Test
       public void test3() {
           Consumer<String> con1 = s -> System.out.println(s);
   
           con1.accept("hello");
   
           System.out.println("---------");
   
           PrintStream out = System.out;
           Consumer<String> con2 = out::println;
           con2.accept("world");
   
       }
   ```

2. 类::静态方法
   ```java
    @Test
       public void test4() {
           Comparator<Integer> integerComparator = (x, y) -> Integer.compare(x, y);
           System.out.println(integerComparator.compare(1, 2));
   
           System.out.println("-----------");
           Comparator<Integer> integerComparator1 = Integer::compare;
           System.out.println(integerComparator1.compare(10, 2));
       }
       @Test
       public void test5() {
           Function<Double,Long> func = d -> Math.round(d);
           System.out.println(func.apply(1.2));
           System.out.println("-----------");
           Function<Double,Long> func1 = Math::round;
           System.out.println(func1.apply(1.7));
       }
   ```

3. 类::非静态方法(当函数式接口方法的第一个参数是需要引用方法的调用者, 第二个参数是需要引用方法的参数(或者无参数)时.)
   ```java
       /*
       Comparator 的 int compare(T t1, T t2)
       和 String 的 int t1.compareTo(t2)
       相当于第一个参数充当了方法的调用者
        */
       @Test
       public void test6() {
           Comparator<String> comparator = (s1, s2) -> s1.compareTo(s2);
           System.out.println(comparator.compare("abc", "abe"));
           System.out.println("-----------");
           Comparator<String> comparator1 = String::compareTo;
           System.out.println(comparator1.compare("abc", "abf"));
       }
   ```

   

**构造器引用**

和方法引用类似，函数式接口的抽象方法的形参列表 和 构造器的形参列表一致。

抽象方法的返回值类型 即为 构造器所属的类的类型

```java
    /*
    Suppiler 的 T get()
    无参构造器
     */
    @Test
    public void test7() {
        Supplier<Emp> supplier = () -> new Emp();
        Emp emp = supplier.get();
        emp.setName("sup1");
        System.out.println(emp);
        System.out.println("-----------");
        Supplier<Emp> supplier1 = Emp::new;
        Emp emp1 = supplier1.get();
        emp1.setName("sup2");
        System.out.println(emp1);
    }


    /*
    Funciton 的 R apply(T t)
    带参构造器
     */
    @Test
    public void test8() {
        Function<Integer, Emp> func = id -> new Emp(id);
        Emp emp = func.apply(100);
        System.out.println(emp);
        System.out.println("-----------");
        Function<Integer, Emp> func1 = Emp::new;
        Emp emp1 = func1.apply(101);
        System.out.println(emp1);
    }
```



**数组引用**

```java
 @Test
    public void test9() {
        Function<Integer, String[]> fun = length -> new String[length];
        String[] strings = fun.apply(3);
        System.out.println(Arrays.toString(strings));
        System.out.println("-----------");
        Function<Integer, String[]> fun1 = String[]::new;
        String[] strings1 = fun1.apply(5);
        System.out.println(Arrays.toString(strings1));
    }
```



## Stream API

使用Steam API对集合数据进行操作, 类似于SQL执行查询数据

和 Collection的区别?  Collection是静态的内存数据结构,面向内存. Stream API是有关计算的, 面向CPU

ps: 

1. Stream自己不会存储元素
2. Stream不会改变源对象。相反，他们会返回一个持有结果的新Stream
3. Stream操作是延迟执行的。这意味着他们会等到需要结果的时候才执行

**操作三步骤**

1. 创建Stream
   一个数据源（如：集合、数组），获取一个流
2. 中间操作
   一个中间操作链，对数据源的数据进行处理
3. 终止操作（终端操作）
   一旦执行终止操作，就执行中间操作链，并产生结果。之后，不会再被使用



### 实例化

1. 通过Collection接口
   1. default Stream\<E> Stream() 返回一个顺序流
   2. default Stream\<E> ParallelStream() 返回一个并行流
2. 通过数组
   1. Arrays.stream(arr)
3. 通过Steam的of()
   1. Stream\<Integer> integerStream = Stream.of(1, 2, 3, 4, 5);
4. 创建无限流
   1. Stream.iterate()
   2. Stream.generate()

```java
package com.joriri;

import org.junit.Test;

import java.util.Arrays;
import java.util.List;
import java.util.stream.IntStream;
import java.util.stream.Stream;

/**
 * @Author zxy
 * @Date 2024/12/14
 * @Description StreamAPI使用例子
 */
public class StreamAPIDemo {
    public static void main(String[] args) {
        // 1. 创建流: 通过集合
        List<Emp> emps = EmpData.getEmps();
        Stream<Emp> stream = emps.stream();

        Stream<Emp> paralleStream = emps.parallelStream();

    }

    @Test
    public void test() {
        // 2. 创建流: 通过数组
        int[] arr = {1,2,3,4,5};
//        System.out.println(Arrays.toString(arr));
        IntStream stream = Arrays.stream(arr);
    }

    @Test
    public void test2() {
        // 3. 创建流: Stream的of()
        Stream<Integer> integerStream = Stream.of(1, 2, 3, 4, 5);
    }

    @Test
    public void test3() {
        // 4. 创建无限流
        // 迭代
        // 遍历前十个偶数
        Stream.iterate(0,t -> t+2).limit(10).forEach(System.out::println);

        // 生成
        // 随机生成10个0-1之间的小数
        Stream.generate(Math::random).limit(10).forEach(System.out::println);
    }
}

```



### 中间操作

#### 筛选与切片

| 方法                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| filter(Predicate p) | 接收Lambda，从流中排除某些元素                               |
| distinct()          | 筛选，通过流所生成元素的 hashCode()和 equals()去除重复元素   |
| limit(long maxSize) | 截断流，使其元素不超过给定数量                               |
| skip(long n)        | 跳过元素，返回一个扔掉了前n个元素的流。若流中元素不足n个，则返回一 个空流。与limit(n)互补 |



#### 映射

| 方法                            | 描述                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| `map(Functionf)`                | 接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。 |
| mapToDouble(ToDoubleFunction f) | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 DoubleStream。 |
| mapTolnt(TolntFunction f)       | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 IntStream。 |
| mapToLong(ToLongFunction f)     | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的LongStream。 |
| `flatMap(Function f)`           | 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流 |

map和flatMap的区别就跟list的append和extend一样.



#### 排序

| 方法                   | 描述                               |
| ---------------------- | ---------------------------------- |
| sorted()               | 产生一个新流，其中按自然顺序排序   |
| sorted(Comparator com) | 产生一个新流，其中按比较器顺序排序 |



### 终止操作

#### 匹配与查找

| 方法                   | 描述                     |
| ---------------------- | ------------------------ |
| allMatch(Predicate p)  | 检查是否匹配所有元素     |
| anyMatch(Predicate p)  | 检查是否至少匹配一个元素 |
| noneMatch(Predicate p) | 检查是否没有匹配所有元素 |
| findFirst()            | 返回第一个元素           |
| findAny()              | 返回当前流中的任意元素   |

| 方法                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| count()             | 返回流中元素总数                                             |
| max(Comparator c)   | 返回流中最大值                                               |
| min(Comparator c)   | 返回流中最小值                                               |
| forEach(Consumer c) | 内部迭代(使用Collection接口需要用户去做迭代， 称为外部迭代。相反，StreamAPI使用内部迭 代 它帮你把迭代做了) |



#### 规约

| 方法                                 | 描述                                                  |
| ------------------------------------ | ----------------------------------------------------- |
| reduce(T identity, BinaryOperator b) | 可以将流中元素反复结合起来，得到一 个值。返回T        |
| reduce(BinaryOperator b)             | 可以将流中元素反复结合起来，得到一 个值。返回Optional |

map和reduce的连接成为map-reduce模式, 谷歌用其来进行网络搜索



#### 收集

| 方法                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| collect(Collector c) | 将流转换为其他形式。接收一个Collector 接口的实现，用于给Stream中元素做汇总的方法 |

Collectors

1. toList
2. toSet
3. toCollection



## Optional

**创建**

1. Optional.of(T t) : 创建一个Optional实例, t必须非空
2. Optional.empty() : 创建一个空的实例
3. Optional.ofNullable(T t): t可以为空

**判断Optional是否包含对象**

1. boolean isPresent()
2. void ifPresent(Consumer<? super T> consumer): 如果有值,就执行Consumer接口的实现代码, 且该值会作为参数传给它

**获取Optional容器的对象**

1. T get(): 如果调用对象包含值, 返回, 否则抛出异常
2. T orElse(T other): 有值返回, 无值返回other对象
3. T orElseGet(Supplier <? extends T> other): 有值返回, 否则返回由Supplier接口实现类提供的对象
4. T orElseThrow(Supplier <? extends X> exceptinSupplier): 有值返回, 返走抛出由Supplier接口实现类提供的异常

