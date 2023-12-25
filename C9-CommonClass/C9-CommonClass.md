# 常用类

## 包装类

包装类(Wrapper)是指基于对java中8种基本数据类型，封装了操作其类型的数据的**多种方法**的JDK原生类，即提供了很多轮子使我们可以直接调用，提升开发效率

| 包装类      | 基本数据类型 |
| :---------- | :----------- |
| `Boolean`   | `boolean`    |
| `Byte`      | `byte`       |
| `Short`     | `short`      |
| `Integer`   | `int`        |
| `Long`      | `long`       |
| `Float`     | `float`      |
| `Double`    | `double`     |
| `Character` | `char`       |

部分包装类和部分包装之间是有一些共性的:

<img width="655" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/a99d4934-c94a-43b6-ac62-a51e00abcf44">


我们可以看到**有关数字**的包装类均实现了**Comparable、Serializable**接口，同时继承了**Number**类

<img width="654" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/659799a1-e0ac-45dd-bf9e-f308f30c236f">

<img width="653" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/50762239-ab5a-4782-a8f3-95c5e138f053">


而与数字无关的包装类Boolean和Character均实现了**Serializable**与**Comparable**接口

### 包装类与基本数据类型之间的转换

以**Integer**与**int**之间的转换为例:

在学习这一点前，我们先了解两个根据**包装**一词而来的术语:

- 装箱: 指**基本数据类型到包装类**的转换
- 拆箱: 之**包装类到基本数据类型**的转换

在JDK5之前，装箱和拆箱均需要编码手动实现，而在JDK5及其之后

#### 装箱

手动装箱:

```java
int int1 = 10;
// 方式一: 将int类型的变量传入Integer的构造器
Integer integer = new Integer(int1); 
// 方式二: 调用valueOf()方法
Integer integer1 = Integer.valueOf(int1); 
```

通过查看Integer重写的`valueOf()`方法的源码，我们发现其实只是在和方式一一样new对象前加了一个判断逻辑，这里是Integer类将值为-128~127的对象已经创建好了，故可以直接返回:

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high) // -128~127内直接返回
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

自动装箱:

自动装箱即通过在JDK底层实现手动装箱的逻辑从而上层操作

```java
int int2 = 20;
Integer integer2 = int2;
```

#### 拆箱

手动拆箱:

```java
int i = integer.intValue();
```

自动拆箱:

同样的，自动拆箱也是通过在底层实现手动的逻辑由此方便上层操作，底层仍然使用的是`intValue()`方法

```java
int int2 = 20;
Integer integer2 = int2;
int int3 = integer2
```

其他包装类与其对应的基本数据类型之间的装箱与拆箱与上述例子中的操作基本相同，因为其他包装类都重写了父类Number中的对应数据类型的方法
<img width="314" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/be517cc3-0b0f-4f99-aaa8-3946a97a1d6c">


### 包装类与String之间的相互转换

以Interger类与String类为例，Integer转StringJDK原生支持的有三种方式

```java
public static void main(String[] args) {
    Integer num = 10;
    // 方式一，和int转String类似
    String numStr = num + " ";
    // 方式二
    String numStr1 = num.toString();
    // 方式三
    String numStr2 = String.valueOf(num);
}
```

String转Integer:

需要注意的是，在实际应用时，转换前我们应加上**判断能否转换为String**的逻辑或异常处理逻辑

有两种方式

```java
String str = "123";
// 方式一，改方法在底层上是应用的是自动装箱
Integer i = Integer.parseInt(str);
// 方式二，Integer的构造器也支持直接传入一个String类型
Integer i1 = new Integer(str);
```

### String类

String类实例化的对象用于存储字符串，即一组字符序列

String这个类或者说一个集合在任何语言中被封装出来都是非常有必要的，虽然char类型已经赋予了语言去适配Ascll码除数字外的字符，但在实际的业务场景中仅使用char去做字符的载体会有大量的重复工作，而String的出现封装的能力之一就是**集合了一"组"字符序列**，这无疑在编程语言演进的过程中极大的提升了开发效率

且如今的编程语言中的String类型，基本均支持Unicode编码，**一个字符占两个字节**

<img width="652" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/22e16a0d-9f29-45d3-b1e1-503034ad716f">


String类和其他包装类一样也实现了Serializable和Comparable接口，且实现了**CharSequence**接口

这里简单的记录一下Serializable接口和Comparable接口的实现意义:

- 实现Serializable接口，则代表该类的对象是可以**被串行化**，从而进行网络传输等I/O操作
- 实现Comparable接口，则代表该类的对象可以进行**比较运算**

#### Stirng类特点

- **具备非常多的构造器**

| 构造器签名                                                   | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `String()`                                                   | 创建一个空字符串对象。                                       |
| `String(String original)`                                    | 根据指定的字符串 `original` 创建一个新的字符串对象，内容与 `original` 相同。 |
| `String(char[] value)`                                       | 根据字符数组 `value` 创建一个新的字符串对象。                |
| `String(char[] value, int offset, int count)`                | 根据字符数组 `value` 的指定部分创建一个新的字符串对象。      |
| `String(byte[] bytes)`                                       | 根据字节数组 `bytes` 创建一个新的字符串对象，使用平台默认的字符集进行解码。 |
| `String(byte[] bytes, int offset, int length)`               | 根据字节数组 `bytes` 的指定部分创建一个新的字符串对象，使用平台默认的字符集进行解码。 |
| `String(byte[] bytes, Charset charset)`                      | 根据字节数组 `bytes` 创建一个新的字符串对象，使用指定的字符集进行解码。 |
| `String(byte[] bytes, int offset, int length, Charset charset)` | 根据字节数组 `bytes` 的指定部分创建一个新的字符串对象，使用指定的字符集进行解码。 |

- String类中有一个私有属性`value`，被final修饰，也就意味着我们**不能修改一个String对象**，即String类的对象可以承载的是**字符串常量**，这句话的含义时，我们不能让已创建完成的字符串对象指向另一个对象，但该对象中的元素我们是可以修改的

- String是一个final类

常用的有两种创建String对象的方式

1. 直接赋值，比如`String name = "ryan";`

   在底层实现上，JVM会先从**常量池**查看是否有值"ryan"的数据空间，如果有则直接将对象指向它，否则则会在常量池创建这个值，再指向它，也就是说最终对象都会指向的是**常量池中的地址**

2. 调用构造器，比如`String name = new String("ryan");`

   在底层实现上，会现在堆中创建空间，其中**维护了value属性**，同样也去常量池中查看是否有值"ryan"的空间，若有则**直接通过value指向**，否则创建这个值再指向它，堆中会存有对象最终指向的地址

#### String类常用方法

| 方法签名                                                     | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `int length()`                                               | 返回字符串的长度。                                           |
| `boolean isEmpty()`                                          | 检查字符串是否为空（长度为 0）。                             |
| `char charAt(int index)`                                     | 返回指定索引位置的字符。                                     |
| `int indexOf(int ch)`                                        | 返回指定字符第一次出现的索引位置。                           |
| `int indexOf(int ch, int fromIndex)`                         | 返回指定字符从指定索引开始第一次出现的索引位置。             |
| `int indexOf(String str)`                                    | 返回指定字符串第一次出现的索引位置。                         |
| `int indexOf(String str, int fromIndex)`                     | 返回指定字符串从指定索引开始第一次出现的索引位置。           |
| `int lastIndexOf(int ch)`                                    | 返回指定字符最后一次出现的索引位置。                         |
| `int lastIndexOf(int ch, int fromIndex)`                     | 返回指定字符从指定索引开始最后一次出现的索引位置。           |
| `int lastIndexOf(String str)`                                | 返回指定字符串最后一次出现的索引位置。                       |
| `int lastIndexOf(String str, int fromIndex)`                 | 返回指定字符串从指定索引开始最后一次出现的索引位置。         |
| `String substring(int beginIndex)`                           | 返回从指定索引开始到字符串末尾的子字符串。                   |
| `String substring(int beginIndex, int endIndex)`             | 返回从指定的开始索引到指定的结束索引之间的子字符串。         |
| `boolean startsWith(String prefix)`                          | 检查字符串是否以指定的前缀开头。                             |
| `boolean startsWith(String prefix, int toffset)`             | 检查字符串从指定索引开始是否以指定的前缀开头。               |
| `boolean endsWith(String suffix)`                            | 检查字符串是否以指定的后缀结尾。                             |
| `boolean contains(CharSequence sequence)`                    | 检查字符串是否包含指定的字符序列。                           |
| `String replace(char oldChar, char newChar)`                 | 将字符串中的所有旧字符替换为新字符。                         |
| `String replace(CharSequence target, CharSequence replacement)` | 将字符串中所有匹配目标字符序列的部分替换为指定的替换字符序列。 |
| `String trim()`                                              | 去除字符串开头和结尾的空白字符。                             |
| `String toLowerCase()`                                       | 将字符串转换为小写形式。                                     |
| `String toUpperCase()`                                       | 将字符串转换为大写形式。                                     |
| `String[] split(String regex)`                               | 使用指定的正则表达式作为分隔符，将字符串拆分为子字符串数组。 |
| `boolean equals(Object obj)`                                 | 检查字符串是否与指定的对象相等。                             |
| `boolean equalsIgnoreCase(String anotherString)`             | 检查字符串是否与指定的字符串忽略大小写后相等。               |
| `int compareTo(String anotherString)`                        | 按字典顺序比较字符串与另一个字符串。                         |
| `int compareToIgnoreCase(String str)`                        | 按字典顺序比较字符串与另一个字符串（忽略大小写）。           |
| `boolean contains(CharSequence sequence)`                    | 检查字符串是否包含指定的字符序列。                           |
| `boolean matches(String regex)`                              | 检查字符串是否与指定的正则表达式匹配。                       |
| `String[] split(String regex)`                               | 使用指定的正则表达式作为分隔符，将字符串拆分为子字符串数组。 |
| `String[] split(String regex, int limit)`                    | 使用指定的正则表达式作为分隔符，将字符串拆分为子字符串数组，限制拆分的数量。 |
| `boolean startsWith(String prefix)`                          | 检查字符串是否以指定的前缀开头。                             |

#### StringBuffer类

String类由于无法修改对象的引用，且在每次修改其中的元素时都需要**重新开辟新的内存并修改地址**，效率较低，为了**提升修改字符串的效率**，JDK中提供了StringBuilder类和StringBuffer类，它们其中的很多方法都与String类中的方法用法相同，但是字符串**变量**的容器，在修改数据时不必每次都更新地址

StringBuffer的特点是**长度可变**，其核心的方法是增删改查:

- `append`，也就是追加元素，还有`insert`，插入元素
- `delete(start, end)`，删除某两个索引之间的元素
- `replace(start, end, string)`，将某两个索引之间的内容用String替换掉
- `indexOf`，查找子串在字符串中按索引升序第一次出现的位置的索引，找不到则返回-1

<img width="652" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/e38659a3-1800-4925-a28e-53f1feacd2fd">


通过其类图我们可见相对于String类，StringBuffer类多实现了**Appendable**接口，赋予其长度可变的能力，同时继承的**AbstractStringBuffer**抽象类中也有为什么StringBuffer类可以被修改的关键因素，其和String类一样都有一个属性`char[] value`，但并没有被final修饰

在实际开发过程中，如果一个字符串可能会被修改，那么为了提升效率我们可以开辟一个StringBuffer的对象，以下是通过调用StringBuffer构造器的方式:

```java
String str = "today";
StringBuffer stringBuffer = new StringBuffer(str);
```

也可以直接追加给一个空的StringBuffer对象:

```java
String str = "today";
StringBuffer stringBuffer = new StringBuffer();
stringBuffer = stringBuffer.append(str);
```

根据StringBuffer对象再开辟一个和其值相同的String对象，可以调用很多包装类都有的`toString()`方法:

```java
StringBuffer stringBuffer = new StringBuffer("today");
String str = stringBuffer.toString();
```

#### StringBuilder类

StringBuilder类经过初步了解，是基于一种**安全换效率**的设计理念所创建**效率上更快，但不是线程安全的**的StringBuffer类，它兼容StringBuffer类中所有的API，应用的场景以字符串缓冲区被**单个线程**使用时为主



| 特性                 | String                                                       | StringBuffer                                                 | StringBuilder                                                |
| :------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 可变性               | 不可变（Immutable）                                          | 可变（Mutable）                                              | 可变（Mutable）                                              |
| 线程安全性           | 线程安全                                                     | 线程安全                                                     | 线程不安全                                                   |
| 性能                 | 低，但复用率高                                               | 中等                                                         | 高                                                           |
| 适用场景             | 适用于字符串常量或不经常修改的字符串                         | 适用于线程安全的字符串操作                                   | 适用于单线程环境下的字符串操作                               |
| 内存使用             | 每次操作都会创建一个新的字符串对象，旧对象会被垃圾回收       | 内部使用字符数组进行操作，不会频繁创建新对象                 | 内部使用字符数组进行操作，不会频繁创建新对象                 |
| 字符串拼接           | 使用 `+` 运算符或 `concat()` 方法，**每次拼接都会创建新的对象** | 使用 `append()` 方法，无需创建新对象，直接在原对象上进行拼接 | 使用 `append()` 方法，无需创建新对象，直接在原对象上进行拼接 |
| 多线程环境下的安全性 | 由于不可变性，可以在多线程环境下安全共享                     | 由于线程安全性，可以在多线程环境下安全共享                   | 在多线程环境下不安全，需要额外的同步保证线程安全             |

## Math类

和其他语言中的Math包中提供的方法大差不差，以下对部分API做了个汇总，基本都是静态方法，是名副其实的工具类:

| 方法签名                    | 描述                                                |
| --------------------------- | --------------------------------------------------- |
| `abs(int a)`                | 返回参数的绝对值（整数）。                          |
| `abs(long a)`               | 返回参数的绝对值（长整数）。                        |
| `abs(float a)`              | 返回参数的绝对值（浮点数）。                        |
| `abs(double a)`             | 返回参数的绝对值（双精度浮点数）。                  |
| `max(int a, int b)`         | 返回两个参数中的较大值（整数）。                    |
| `max(long a, long b)`       | 返回两个参数中的较大值（长整数）。                  |
| `max(float a, float b)`     | 返回两个参数中的较大值（浮点数）。                  |
| `max(double a, double b)`   | 返回两个参数中的较大值（双精度浮点数）。            |
| `min(int a, int b)`         | 返回两个参数中的较小值（整数）。                    |
| `min(long a, long b)`       | 返回两个参数中的较小值（长整数）。                  |
| `min(float a, float b)`     | 返回两个参数中的较小值（浮点数）。                  |
| `min(double a, double b)`   | 返回两个参数中的较小值（双精度浮点数）。            |
| `sqrt(double a)`            | 返回参数的平方根。                                  |
| `cbrt(double a)`            | 返回参数的立方根。                                  |
| `pow(double a, double b)`   | 返回a的b次方。                                      |
| `exp(double a)`             | 返回自然对数的底e的指数。                           |
| `log(double a)`             | 返回参数的自然对数。                                |
| `log10(double a)`           | 返回参数的以10为底的对数。                          |
| `toDegrees(double angrad)`  | 将弧度转换为角度。                                  |
| `toRadians(double angdeg)`  | 将角度转换为弧度。                                  |
| `sin(double a)`             | 返回角的正弦值。                                    |
| `cos(double a)`             | 返回角的余弦值。                                    |
| `tan(double a)`             | 返回角的正切值。                                    |
| `asin(double a)`            | 返回角的反正弦值。                                  |
| `acos(double a)`            | 返回角的反余弦值。                                  |
| `atan(double a)`            | 返回角的反正切值。                                  |
| `atan2(double y, double x)` | 将直角坐标(x, y)转换为极坐标(r, theta)并返回theta。 |
| `sinh(double x)`            | 返回双曲正弦值。                                    |
| `cosh(double x)`            | 返回双曲余弦值。                                    |
| `tanh(double x)`            | 返回双曲正切值。                                    |
| `expm1(double x)`           | 返回e的x次方减1的值。                               |
| `log1p(double x)`           | 返回参数加1的自然对数。                             |

比较常见的还有`random`方法，其不支持输入参数，返回的是0~1之间的数:

我们可以通过运算得到理想范围内的数，比如12 ~ 15:

```java
System.out.println(12 + Math.random()*3);
```

## Arrays类

其中包含的也基本是静态方法，也是一个工具类，主要用于管理和操作数组，比如排序、搜索、填充、比较等

| 方法签名                                              | 描述                                                         |
| :---------------------------------------------------- | :----------------------------------------------------------- |
| `static void sort(int[] a)`                           | 对指定的整型数组按升序进行排序。                             |
| `static void sort(T[] a)`                             | 对指定的对象数组按升序进行排序。其中，类型参数 `T` 是数组中元素的类型。 |
| `static void sort(T[] a, Comparator<? super T> c)`    | 根据指定的比较器对对象数组进行排序。其中，类型参数 `T` 是数组中元素的类型。 |
| `static int binarySearch(int[] a, int key)`           | 在指定的整型数组中使用二分查找算法搜索指定的键值。如果找到，返回键值的索引；否则，返回负数。 |
| `static int binarySearch(Object[] a, Object key)`     | 在指定的对象数组中使用二分查找算法搜索指定的键值。如果找到，返回键值的索引；否则，返回负数。 |
| `static boolean equals(int[] a, int[] a2)`            | 检查两个整型数组是否相等。                                   |
| `static boolean equals(Object[] a, Object[] a2)`      | 检查两个对象数组是否相等。                                   |
| `static void fill(int[] a, int val)`                  | 使用指定的值填充整型数组的所有元素。                         |
| `static void fill(Object[] a, Object val)`            | 使用指定的值填充对象数组的所有元素。                         |
| `static List<T> asList(T... a)`                       | 将指定的数组转换为 List。其中，类型参数 `T` 是数组中元素的类型。 |
| `static int hashCode(int[] a)`                        | 返回整型数组的哈希码值。                                     |
| `static int hashCode(long[] a)`                       | 返回长整型数组的哈希码值。                                   |
| `static String toString(int[] a)`                     | 返回整型数组的字符串表示形式。                               |
| `static String toString(long[] a)`                    | 返回长整型数组的字符串表示形式。                             |
| `static void copyOf(int[] original, int newLength)`   | 将指定的整型数组复制到一个新数组中，新数组的长度为指定的长度。 |
| `static void copyOf(T[] original, int newLength)`     | 将指定的对象数组复制到一个新数组中，新数组的长度为指定的长度。其中，类型参数 `T` 是数组中元素的类型。 |
| `static void sort(T[] a, int fromIndex, int toIndex)` | 对指定对象数组的指定范围进行排序。其中，类型参数 `T` 是数组中元素的类型。 |

其中，`static void sort(T[] a, Comparator<? super T> c)`有些类似go中的`sort.Slice()`方法，我们可以自行实现比较的逻辑

`static int binarySearch(int[] a, int key)`在查找不到元素时，会返回的是-(low + 1)，其中low是指要查找的值在数组中的位置的左边界的索引值+1，比如:

```
public static void main(String[] args) {
    Integer[] array = {1, 2, 43, 45, 67, 888};
    System.out.println(Arrays.binarySearch(array,66)); // -(3+1+1)
}
```

## System类

System类中大多是一些涉及底层的方法，比如终止程序(exit)、手动gc(gc)等，令人意外的是，其中也包含了返回当前时间戳的方法(currentTimeMillis)

| 方法签名                                                     | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `static void exit(int status)`                               | 终止当前运行的 Java 虚拟机。                                 |
| `static long currentTimeMillis()`                            | 返回当前时间（以毫秒为单位）的时间戳。                       |
| `static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)` | 将指定源数组中的一个指定范围的元素复制到目标数组的指定位置处。 |
| `static Properties getProperties()`                          | 返回表示当前系统属性的 Properties 对象。                     |
| `static String getProperty(String key)`                      | 获取指定键的系统属性值。                                     |
| `static void setProperty(String key, String value)`          | 设置指定键的系统属性值。                                     |
| `static void gc()`                                           | 强制进行垃圾回收。                                           |
| `static void runFinalization()`                              | 运行已经被丢弃的对象的 finalize 方法。                       |
| `static void load(String filename)`                          | 加载指定名称的动态链接库（本地库）。                         |
| `static void loadLibrary(String libname)`                    | 加载具有指定库名的动态链接库。                               |
| `static String lineSeparator()`                              | 返回系统特定的行分隔符。                                     |
| `static void setErr(PrintStream err)`                        | 将标准错误输出流重定向到指定的输出流。                       |
| `static void setOut(PrintStream out)`                        | 将标准输出流重定向到指定的输出流。                           |
| `static void setIn(InputStream in)`                          | 将标准输入流重定向到指定的输入流。                           |
| `static Console console()`                                   | 返回与当前 Java 虚拟机关联的控制台对象（如果有）。           |

对于今后可能会用到的`static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`，进行参数说明:

- src: 原数组
- srcPos: 从原数组进行拷贝操作的起始索引
- dest: 目标数组
- destPos: 目标数组接收拷贝数据的起始索引
- length: 拷贝的元素个数

## BigInteger类和BigDecimal类

用于存放大数的包装类，解决一些可能会造成**精度不够**的运算问题，需要注意的是大数类的对象可能是因为位溢出的缘故，不能进行通过运算符进行**加减乘除**操作，需要使用其类提供的API(`add`、`subtract`、`multiply`、`divide`)

```java
public static void main(String[] args) {
    BigInteger bigInteger = new BigInteger("99999999999999999999999999999999999999");
    BigInteger bigInteger1 = new BigInteger("123");
    System.out.println(bigInteger.add(bigInteger1));
}
```

## Date类

Data类在JDK8前进行了三代的演化，分别是`Date、`，Date类实现了`Cloneable`、`Comparable`、`Serializable`接口

### 第一代: Date

获取当前时间、时间戳及两者相互转换:

```java
// 获取当前系统的当前时间
Date date = new Date();
System.out.println(date); // Sat Nov 25 13:35:29 CST 2023
// 获取当前时间戳
System.out.println(date.getTime()); // 1700890856793
// 获取时间戳对应的时间
Date date1 = new Date(123890011);
System.out.println(date1); // Fri Jan 02 18:24:50 CST 1970
```

日期格式化:

以下是java中个日期占位符与其表示的含义的对应关系表格，类似go中的"2006-01-02 15:04:05"(go语言诞生的时刻):

| 字母 | 含义                                      | 示例                            |
| :--- | :---------------------------------------- | :------------------------------ |
| y    | 年，一个y代表一位                         | "yyy"代表023，"yyyy"代表2023    |
| M    | 月份                                      | 例如八月，M代表8，MM代表08      |
| w    | 一年中的第几周                            | 常用ww表示                      |
| W    | 一个月中的第几周                          | 常用WW表示                      |
| d    | 一个月中的第几天                          | 常用dd表示                      |
| D    | 一年中的第几天                            | 常用DDD表示                     |
| E    | 星期几，用E表示星期，根据不同语言环境返回 | CHINA表示星期几，US表示英文缩写 |
| a    | 上午或下午                                | am代表上午，pm代表下午          |
| H    | 一天中的小时数，二十四小时制              | 常用HH表示                      |
| h    | 一天中的小时数，十二小时制                | 常用hh表示                      |
| m    | 分钟数                                    | 常用mm表示                      |
| s    | 秒数                                      | 常用ss表示                      |
| S    | 毫秒数                                    | 常用SSS表示                     |

如果我们要以常见的xx年xx月xx日 xx:xx:xx 格式去输出当前时间:

```java
Date date = new Date();
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy年MM月dd日 hh:mm:ss E");
String formatTime = simpleDateFormat.format(date);
System.out.println(formatTime); // 2023年11月25日 01:54:19 周六
```

反之，把一个遵循日期格式的字符串转为Date类的对象，可以使用`parse()`方法

```java
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy年MM月dd日 hh:mm:ss E");
String time = "2002年01月07日 10:20:30 星期二";
Date parsedTime = simpleDateFormat.parse(time); // 要注意抛出异常
System.out.println(parsedTime); // Mon Jan 07 10:20:30 CST 2002
```

### 第二代: Calender

Calender是一个抽象类，且构造器是`private`，创建其对象需要通过`getInstance()`方法获取，其实例化的对象具有大量的信息(静态属性)供我们获取，需要什么取什么即可，相对于第一代省去了格式化的操作

```java
Calendar calendar = Calendar.getInstance();
// 获取当前年份
System.out.println(calendar.get(Calendar.YEAR));
// 获取当前月份(1月是0)
System.out.println(calendar.get(Calendar.MONTH) + 1);
// 当前在月中的第几天
System.out.println(calendar.get(Calendar.DAY_OF_MONTH));
// 当前小时(12小时制)
System.out.println(calendar.get(Calendar.HOUR));
// 当前小时(24小时制)
System.out.println(calendar.get(Calendar.HOUR_OF_DAY));
System.out.println(calendar.get(Calendar.MINUTE));
System.out.println(calendar.get(Calendar.SECOND));
```

需要注意的是，Calender**不是线程安全的**，在多线程场景要慎用

### 第三代: LocalDateTime

标题中是一个概述，第三代包括**LocalDate、LocalTime、LocalDateTime**，是于JDK8引入的

- LocalDate: 只包含日期信息，用于获取日期(年月日)相关字段
- LocalTime: 只包含时间信息，用于获取时间(时分秒)相关字段
- LocalDateTime: 包含日期+时间

其中LocalDateTime的类图如下图所示，可以看到其相对于前两代多实现了很多方法，对于其中的接口的实现意义在日后的学习中再进一步探索:

<img width="650" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/eefcbb88-4073-46ec-b50e-cca3a38f8817">

通过创建一个LocalDateTime对象，可以方便的获取很多常见的时间信息:

```java
// 获取当前日期的对象
LocalDateTime localDateTime = LocalDateTime.now();
System.out.println(localDateTime); // 2023-11-25T14:39:01.937109
// 年
System.out.println(localDateTime.getYear()); // 2023
// 月(英文)
System.out.println(localDateTime.getMonth()); // NOVEMBER
// 月(数字)
System.out.println(localDateTime.getMonthValue()); // 11
// 日
System.out.println(localDateTime.getDayOfMonth()); // 25
```

同时该类的对象对时间相关的计算提供了很多API，比如大量的`plus`、`minus`方法

对于格式化操作，可以通过`DateTimeFormat`类的对象来应用`LocalDateTime`对象

```java
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH小时mm分钟ss秒");
String formatTime = dateTimeFormatter.format(localDateTime);
System.out.println(formatTime); // 2023年11月25日 14小时48分钟24秒
```

常用方法:

| 方法                                                         | 描述                                                        |
| :----------------------------------------------------------- | :---------------------------------------------------------- |
| `static LocalDateTime now()`                                 | 获取默认时区的当前日期时间。                                |
| `static LocalDateTime now(Clock clock)`                      | 从指定时钟获取当前日期时间。                                |
| `static LocalDateTime now(ZoneId zone)`                      | 获取指定时区的当前日期时间。                                |
| `static LocalDateTime of(LocalDate date, LocalTime time)`    | 根据日期和时间对象获取 `LocalDateTime` 对象。               |
| `static LocalDateTime of(int year, Month month, int dayOfMonth, int hour, int minute, int second)` | 根据指定的年、月、日、时、分、秒获取 `LocalDateTime` 实例。 |
| `getYear()`                                                  | 获取年份。                                                  |
| `getMonth()`                                                 | 使用月份枚举类获取月份。                                    |
| `getDayOfMonth()`                                            | 获取日期在该月是第几天。                                    |
| `getDayOfWeek()`                                             | 获取日期是星期几。                                          |
| `getDayOfYear()`                                             | 获取日期在该年是第几天。                                    |
| `getHour()`                                                  | 获取小时。                                                  |
| `getMinute()`                                                | 获取分钟。                                                  |
| `getSecond()`                                                | 获取秒。                                                    |
| `getNano()`                                                  | 获取纳秒。                                                  |
| `plusYears(long years)`                                      | 增加年。                                                    |
| `plusMonths(long months)`                                    | 增加月。                                                    |
| `plusWeeks(long weeks)`                                      | 增加周。                                                    |
| `plusDays(long days)`                                        | 增加天。                                                    |
| `plusHours(long hours)`                                      | 增加小时。                                                  |
| `plusMinutes(long minutes)`                                  | 增加分钟。                                                  |
| `plusSeconds(long seconds)`                                  | 增加秒。                                                    |
| `plusNanos(long nanos)`                                      | 增加纳秒。                                                  |
| `minusYears(long years)`                                     | 减少年。                                                    |
| `minusMonths(long months)`                                   | 减少月。                                                    |
| `minusWeeks(long weeks)`                                     | 减少周。                                                    |
| `minusDays(long days)`                                       | 减少天。                                                    |
| `minusHours(long hours)`                                     | 减少小时。                                                  |
| `minusMinutes(long minutes)`                                 | 减少分钟。                                                  |
| `minusSeconds(long seconds)`                                 | 减少秒。                                                    |
| `minusNanos(long nanos)`                                     | 减少纳秒。                                                  |
| `withYear(int year)`                                         | 替换年份。                                                  |
| `withMonth(int month)`                                       | 替换月份。                                                  |
| `withDayOfMonth(int dayOfMonth)`                             | 替换月份中的第几天（1-31）。                                |
| `withDayOfYear(int dayOfYear)`                               | 替换年份中的第几天（1-366）。                               |
| `withHour(int hour)`                                         | 替换小时。                                                  |
| `withMinute(int minute)`                                     | 替换分钟。                                                  |
| `withSecond(int second)`                                     | 替换秒钟。                                                  |
| `withNano(int nanoOfSecond)`                                 | 替换纳秒。                                                  |
| `isEqual(ChronoLocalDateTime<?> other)`                      | 判断日期时间是否相等。                                      |
| `isBefore(ChronoLocalDateTime<?> other)`                     | 检查是否在指定日期时间之前。                                |
| `isAfter(ChronoLocalDateTime<?> other)`                      | 检查是否在指定日期时间之后。                                |

## 
