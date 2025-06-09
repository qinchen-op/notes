
---

`Stream`是`JDK8` 开始新增的一套API，可以用于操作集合或者数组的数据。

优势：`Stream` 流大量的结合了`Lambda` 的语法风格来编程，功能强大，性能高效，代码简洁，可读性好。

1. 获取`Stream` 流：Stream流代表一条流水线，并能与数据原建立链接
2. 调用流水线的各种方法，对数据进行处理计算。
3. 获取处理的结果，遍历、统计、收集到一个新集合中返回

## 获取`Stream`流

集合：

| `default Stream<E> stream()` | 获取当前集合的`Stream` 流 |
| ---------------------------- | ----------------- |

数组：

| `public static <T> Stream<T> stream(T[] array)` | `Arrays`类 |
| --- | --- |
| `public static<T> Stream<T> of(T... values)`  | `Stream` 类下提供的方法 |

# 中间方法

中间方法时指调用完成后会返回新的`Stream` ，可以继续使用（支持链式编程）

| `Stream<T> filter(Predicate<? super T> predicate)`             | 用于对流中的数据进行过滤     |
| -------------------------------------------------------------- | ---------------- |
| `Stream<T> sorted()`                                           | 对元素进行升序排序        |
| `Stream<T> sorted(Comparator< ? super T > comparator)`         | 按照指定规则排序         |
| `Stream<T> limit(long n)`                                      | 获取前几个元素          |
| `Stream<T> skip(long n)`                                       | 跳过前几个元素          |
| `Stream<T> distinct()`                                         | 去除流中重复的元素        |
| `<R> Stream<T> map(Function< ? super T, ? extends R > mapper)` | 对元素进行加工，并返回对应的新流 |
| `Static Stream<T> concat(Stream a,Stream b)`                   | 合并a与b两个流为一个流     |

# 终结方法

终极方法指的是调用完成后，不会返回新`Stream` 了，没法继续使用流了。

| `void forEach(Consumer action)`                  | 对此流运算后的元素执行遍历 |
| ------------------------------------------------ | ------------- |
| `long count()`                                   | 统计此流运算后的元素个数  |
| `Optional max(Comparator<? super T> comparator)` | 获取此流运算后的最大值元素 |
| `Optional min(Comparator<? super T> comparator)` | 获取此流运算后的最小值元素 |

收集`Stream` 流：就是把Stream流操作后的结果转会到集合或数组中返回

| `R collect(Collector collector)`       | 把流处理后的结果收集到一个指定的集合中去 |
| -------------------------------------- | -------------------- |
| `Object[] toArry()`                    | 把流处理后收集到一个数组中去       |
| `public static <T> Collector toList()` | 收集到list集合中           |
| `public static <T> Collector toSet()`  | 收集到Set集合中            |
| `public static <T> Collector toMap()`  | 收集到Map中              |
