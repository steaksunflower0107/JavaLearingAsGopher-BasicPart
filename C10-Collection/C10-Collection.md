# 集合

集合即是一类赋予了更便捷的操作容器中数据的方法、可被迭代的容器，在存储性上也更加优秀、灵活，并可以集成**不同**数据类型

根据容器中**基本单位的数据个数**，常见集合可以被分为单列集合、双列集合

## 单列集合(Collection接口)

<img width="653" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/a5c0694a-0426-4ef5-ab58-279763a854e0">


可见单列集合都实现了`Collection`接口，且其没有直接的实现类，而是需要去实现`Set`和`List`

`Collection`支持传入的对象类型是`Object`，也就是**任何类型都可以传入，包括null**

`Collection`中规定了一些常见方法，使得单列集合中的类的对象的操作更加便捷:

- `add`: 添加**一个**元素
- `remove`: 删除指定元素
- `contains`: 查找元素是否存在
- `size`: 获取元素个数
- `isEmpty`: 判断是否为空
- `clear`: 清空元素
- `addAll`: 添加多个元素
- `removeAll`: 删除多个元素
- `containsAll`: 查到多个元素是否**全部**存在

以ArrayList为例:

```java
public static void main(String[] args) {
    List list = new ArrayList();
    list.add("string");
    list.add(123);
    list.add(true);
    System.out.println(list); // [string, 123, true]

    list.remove(true);
    System.out.println(list); // [string, 123]

    System.out.println(list.contains(true)); // false
    System.out.println(list.size()); // 2
    System.out.println(list.isEmpty()); // false
    list.clear();
    System.out.println(list.isEmpty()); // true

    ArrayList arrayList = new ArrayList();
    arrayList.add(11.22);
    arrayList.add('a');
    list.addAll(arrayList);
    System.out.println(list); // [11.22, a]

    System.out.println(list.containsAll(arrayList)); // true
}
```

通过类图还不难看出，单列集合的顶级接口是`iterable`，其中的核心是**iterator**，用于返回一个迭代器，只要实现了该接口的集合的对象都可以通过创建一个iterator去遍历集合中的元素

iterator中的核心方法是`next`和`hasNext`，前者是用于遍历到下一个对象并返回它，后者是用于是否还可以去遍历下一个对象

```java
ArrayList arrayList1 = new ArrayList();
arrayList1.add("string");
arrayList1.add(123);
arrayList1.add(123.321);
arrayList1.add(true);

Iterator iterator = arrayList1.iterator();
while (iterator.hasNext()) {
    Object next = iterator.next();
    System.out.println(next);
  	// string
    // 123
    // 123.321
    // true
}
```

之前有在示例中使用过的增强for循环`for each`，即是实现了iterable机制的简化版

### List接口

实现List接口的集合则具备了两个特点:

- 集合中的元素是**有序的**，即其支持索引，且添加顺序和索引值成正比
- 集合中的元素是**可重复的**

```java
public static void main(String[] args) {
    List list = new ArrayList();
    list.add("1");
    list.add("2");
    list.add("3");
    list.add("4");
    list.add("4");
    System.out.println(list); // [1, 2, 3, 4, 4]
  	System.out.println(list.get(2)); // 3
}
```

但通过索引访问某个元素，不能通过类似数组或者golang中的切片一样的`数组名[索引]`形式去访问，需要通过`get()`方法

List接口中常见的方法有:

| 方法签名                                               | 描述                                                         |
| :----------------------------------------------------- | :----------------------------------------------------------- |
| `boolean add(E e)`                                     | 将指定元素添加到列表的末尾。                                 |
| `void add(int index, E element)`                       | 在指定位置插入指定元素。                                     |
| `boolean addAll(Collection<? extends E> c)`            | 将指定集合中的所有元素添加到列表的末尾。                     |
| `boolean addAll(int index, Collection<? extends E> c)` | 将指定集合中的所有元素插入到列表的指定位置。                 |
| `void clear()`                                         | 从列表中移除所有元素。                                       |
| `boolean contains(Object o)`                           | 如果列表包含指定的元素，则返回 `true`。                      |
| `boolean containsAll(Collection<?> c)`                 | 如果列表包含指定集合中的所有元素，则返回 `true`。            |
| `boolean equals(Object o)`                             | 比较列表与指定对象是否相等。                                 |
| `E get(int index)`                                     | 返回列表中指定位置的元素。                                   |
| `int hashCode()`                                       | 返回列表的哈希码值。                                         |
| `int indexOf(Object o)`                                | 返回列表中首次出现指定元素的索引，如果列表不包含该元素，则返回 -1。 |
| `boolean isEmpty()`                                    | 如果列表不包含任何元素，则返回 `true`。                      |
| `Iterator<E> iterator()`                               | 返回在列表上进行迭代的迭代器。                               |
| `int lastIndexOf(Object o)`                            | 返回列表中最后出现指定元素的索引，如果列表不包含该元素，则返回 -1。 |
| `ListIterator<E> listIterator()`                       | 返回列表中的列表迭代器（按适当顺序）。                       |
| `ListIterator<E> listIterator(int index)`              | 从列表中的指定位置开始，返回列表中的列表迭代器。             |
| `boolean remove(Object o)`                             | 从列表中移除首次出现的指定元素（如果存在）。                 |
| `E remove(int index)`                                  | 移除列表中指定位置的元素。                                   |
| `boolean removeAll(Collection<?> c)`                   | 从列表中移除包含在指定集合中的所有元素。                     |
| `boolean retainAll(Collection<?> c)`                   | 仅保留列表中包含在指定集合中的元素，移除其他元素。           |
| `E set(int index, E element)`                          | 用指定元素替换列表中指定位置的元素。                         |
| `int size()`                                           | 返回列表中的元素数量。                                       |
| `List<E> subList(int fromIndex, int toIndex)`          | 返回列表中指定范围的部分视图。                               |
| `Object[] toArray()`                                   | 返回包含列表中所有元素的数组。如果列表的运行时类型不是 `Object[]`，则返回一个新的数组。 |
| `<T> T[] toArray(T[] a)`                               | 返回包含列表中所有元素的数组；如果指定数组足够大，则将元素填充到指定数组中。 |

可以看到很多方法都重写了`Collection`接口中所有的方法

#### List接口常见实现类

常见的实现类有**ArrayList、LinkedList、Vector、Stack**等

| 特点         | ArrayList                                    | Vector                                       | LinkedList                                     |
| :----------- | :------------------------------------------- | :------------------------------------------- | :--------------------------------------------- |
| 底层数据结构 | 动态数组                                     | 动态数组                                     | 双向链表                                       |
| 线程安全性   | **非线程安全**                               | 线程安全                                     | 非线程安全                                     |
| 扩容机制     | 每次增长原容量的一半                         | 每次增长原容量的一倍                         | 不需要扩容，可以根据需要动态分配和释放内存     |
| 遍历效率     | 高效的随机访问和遍历                         | 高效的随机访问和遍历                         | 低效的随机访问，高效的插入、删除和操作         |
| 内存占用     | 相对较小                                     | 相对较大                                     | 相对较大                                       |
| 插入和删除   | 在末尾插入和删除元素高效，中间插入和删除较慢 | 在末尾插入和删除元素高效，中间插入和删除较慢 | 在任意位置插入和删除元素高效                   |
| 适用场景     | 适用于频繁访问和随机访问，不涉及多线程操作   | 适用于频繁访问和随机访问，需要线程安全       | 适用于频繁的插入、删除和操作，不涉及多线程操作 |

**ArrayList**在设计上类似于StringBuilder的设计理念，即用数据安全性换取执行效率

当使用无参构造器创建创建ArrayList的对象时，**初始容量为0**，在第一次有元素添加时，**容量扩容为10(DEFAULT_CAPACITY)**，后续再次扩容则每次扩充原容量的50%，这里的容量的概念类似于go中Slice中的capacity，扩容使用的是`Array.copyOf()`方法

当使用的`ArrayList(int)`构造器创建对象，则可以通过传入的参数指定Arraylist的初始容量

**Vector**类的方法带有`synchronized`修饰，即是线程同步的，方法使用起来和ArrayList类似，**Vector的DEFAULT_CAPACITY也是10**

**LinkedList**的底层是双向链表，那么可以很快想到的是用其实现LRU可能是一个很合适的场景

其中主要是维护了`first`和`last`两个属性，分别指向链表的头结点和尾结点，对于链表中的`Node`的对象，维护了`prev`、`next`、`item`三个属性，前两者分别指向前一个元素和后一个元素，而item是当前结点的值

既然是链表，那么其对于删除和添加的操作不需要发送值拷贝，故**相对于数组实现的效率较高**

### Set接口

实现了Set接口的集合的特点是

- **无序**，即添加和取出的顺序可能是不一致的，**即没有相关性**，没有索引的概念，**但取出的顺序是固定的**，即集合的元素被整合后有一固定顺序，是幂等的

  ```java
  public static void main(String[] args) {
      Set set = new HashSet();
      set.add(3);
      set.add(5);
      set.add(1);
      set.add(2);
      set.add(4);
  
      for (int i = 0; i < 10; i++) {
          System.out.println(set);
      }
      /*
      输出:
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
          [1, 2, 3, 4, 5]
       */
  }
  ```

  既然是无序的，遍历Set接口的实现类是就无法使用访问索引的方式去变量，而只能使用**增强for**和**迭代器**

- **不允许有重复的元素**，但这并不是在编译层面的规则，比如Set的实现类HashSet中重写的`add()`方法:

  ```java
  boolean add(E e);
  ```

  根据该方法签名我们看到add返回的是一个布尔值，用于判断是否**添加成功**，添加集合中已有的元素不会添加成功，值得指出的是因此最多只能有一个**null**值

  重复的判断规则是**是否最终指向同一块内存**，因此如执行`add(new Person("ryan"))`再执行一次会被认为均是添加成功的操作，因为两者在堆上的内存地址是不一样的

#### Set接口的常见实现类

常见的实现类有**HashSet、LinkedHashSet、TreeSet**等

对于**HashSet**:

- HashSet的底层是基于HashMap实现的，HashSet中的对象存储在Map的key中，而value由**对象常量(PRESENT)**填充，其底层维护的结构是数组+链表+红黑树
- 在添加元素时，会先经过hash运算得到其hash值，再通过某种运算转为table数组的索引值
- 查找table数组中的该索引位置是否已经有元素，若没有，则直接加入该元素
- 若有的话，则会调用`equals`方法比较两者，相同则放弃添加，不同则添加到该索引位置的链表尾部

其具体的扩容机制是:

- 第一次添加时，table数组的容量扩充到16，临界值(threshold)是16 * 0.75(加载因子，loadFactor) = 12
- 如果table数组在使用过程中超过了临界值12，则会**翻倍扩容**，则新的临界值是32 * 0.75 = 24，一次类推
- 在java8中，如果某个索引位置的链表元素个数超过了TREEIFY_THRESHOLD(默认值是8)，且table的大小 >= MIN_TREEIFY_CAPACITY(默认值是64)，则该条链表会**树化成一颗红黑树**，否则仍然采用数组的扩容机制

对于**LinkedHashSet**:

- LinkedHashSet的底层是基于LinkedHashMap实现的，底层维护的结构是数组+双向链表，每一个结点有`pre`和`next`属性，链表中有`head`和`tail`指针
- LinkedHashSet**继承了**HashSet
- 元素在LinkedHashSet中的HashTable的存储位置是由元素的HashCode值所决定的，添加的判断机制和HashSet相同，链表维护了元素的次序，由此遍历的顺序会和插入的顺序相同

对于**TreeSet**:

- TreeSet是实现Set接口的类中支持有序排列key的集合，但使用无参构造器创建TreeSet对象时，key仍然是无序的

- TreeSet具有支持传入比较规则的构造器，比如我们加入比较字符串的排序逻辑:

  ```java
  public static void main(String[] args) {
      TreeSet treeSet = new TreeSet(new Comparator() {
          @Override
          public int compare(Object o1, Object o2) {
              return ((String) o1).compareTo((String) o2) ;
          }
      });
  
      treeSet.add("b");
      treeSet.add("f");
      treeSet.add("a");
      treeSet.add("d");
  
      System.out.println(treeSet);
    	// 输出:
    	// [a, b, d, f]
  }
  ```

  且这里的`Comparator`同时也与**元素能否被添加有关**，比如我们如果在compare方法中注入的是长度相关的逻辑:

  ```java
  TreeSet treeSet = new TreeSet(new Comparator() {
      @Override
      public int compare(Object o1, Object o2) {
          return ((String) o1).length() - ((String) o2).length();
      }
  });
  ```

  那么在以上比较器的限定规则中，某元素的长度若在集合中已经存在，则不能添加

## 双列集合(Map接口)

<img width="653" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/33bfb959-f904-40be-bfeb-3fb22b694196">


可见双列集合均实现了Map接口

同大多数语言一样，Map是java中存储**具有映射关系**的键值对的数据结构，其中key和value可以是任何引用类型的对象，即Object类型

主要的特点有:

- 正如之前的篇幅所有叙述的Set接口底层基于HashMap中所提及的元素添加机制一样，Map中的key不允许重复(**覆盖**机制)，但value是可以的，为null是可以的

- 每一对key-value均存放在一个**HashMap.Node**类的对象中

  ```java
  HashMap$Node node = newNode(hash, key, value, null);
  ```

  该类实现了Map.Entry接口， 该接口的实现意义是为了**方便遍历**，其规定了两个方法`getKey`、`getValue`，分别用于访问键、值

Map接口的常见方法:

| 方法签名                                                     | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `int size()`                                                 | 返回 Map 中键值对的数量。                                    |
| `boolean isEmpty()`                                          | 判断 Map 是否为空。                                          |
| `boolean containsKey(Object key)`                            | 判断 Map 是否包含指定的键。                                  |
| `boolean containsValue(Object value)`                        | 判断 Map 是否包含指定的值。                                  |
| `V get(Object key)`                                          | 根据键获取对应的值。如果键不存在，则返回 null。              |
| `V put(K key, V value)`                                      | 向 Map 中添加键值对。如果键已存在，则用新值替换旧值，并返回旧值。 |
| `V remove(Object key)`                                       | 根据键移除对应的键值对，并返回被移除的值。如果键不存在，则返回 null。 |
| `void putAll(Map<? extends K, ? extends V> m)`               | 将指定 Map 中的所有键值对添加到当前 Map 中。                 |
| `void clear()`                                               | 清空 Map，移除所有的键值对。                                 |
| `Set<K> keySet()`                                            | 返回包含所有键的 Set 集合。                                  |
| `Collection<V> values()`                                     | 返回包含所有值的 Collection 集合。                           |
| `Set<Map.Entry<K, V>> entrySet()`                            | 返回包含所有键值对的 Set 集合，每个键值对表示为 Map.Entry 实例。 |
| `default V getOrDefault(Object key, V defaultValue)`         | 根据键获取对应的值，如果键不存在，则返回指定的默认值。       |
| `default void forEach(BiConsumer<? super K, ? super V> action)` | 对 Map 中的每个键值对执行指定的操作。                        |
| `default V putIfAbsent(K key, V value)`                      | 向 Map 中添加键值对，如果键不存在，则添加；如果键已存在，则不做任何操作。并返回旧值或者 null。 |
| `default boolean remove(Object key, Object value)`           | 根据键值对移除指定的键值对，只有在键和值都匹配时才会被移除。 |
| `default boolean replace(K key, V oldValue, V newValue)`     | 替换指定键的值，只有在旧值匹配时才进行替换，并返回替换是否成功的结果。 |
| `default V replace(K key, V value)`                          | 替换指定键的值，并返回旧值。如果键不存在，则返回 null。      |
| `default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function)` | 对 Map 中的每个键值对执行指定的操作，并用操作的结果替换原有的值。 |

### Map的遍历

对于实现Map接口的类的对象的遍历，可以使用增强for、迭代器，或者使用`keySet()`、`Value`方法获取map中所有的key、value

使用增强for:

```java
public static void main(String[] args) {
    Map map = new HashMap();
    map.put(1, "1");
    map.put(2, "2");
    map.put(3, "3");

    for(Object key: map.keySet()) {
        System.out.println(map.get(key));
    }
}
```

使用迭代器(借助EntrySet):

```java
public static void main(String[] args) {
    Map map = new HashMap();
    map.put(1, "1");
    map.put(2, "2");
    map.put(3, "3");

    Set entrySet = map.entrySet();
    Iterator iterator = entrySet.iterator();

    while (iterator.hasNext()) {
        Map.Entry entry = (Map.Entry)iterator.next();
        System.out.println(entry.getValue());
    }
}
```

### Map接口常见实现类

除了之前例子中有使用的HashMap，Map接口的常见实现类还有**HashTable**，它继承了Dictionary类，其与HashMap的主要区别是:

- HashTable**是线程安全的(synchronized)**
- HashTable的键值**均不能为NULL**，否则会抛出空指针异常

它的底层数组类似HashMap，是Hashtable&Entry，其默认大小为11，临界值(threshold)为 11 * 0.75 = 8，在添加元素时，底层执行的方法是`addEntry(hash, key, value, index)`

以下是HashMap和HashTable的详细比对:

| 特性                 | HashMap                                                      | Hashtable                                            |
| :------------------- | :----------------------------------------------------------- | :--------------------------------------------------- |
| 线程安全             | 否                                                           | 是                                                   |
| 键/值是否允许为 null | 允许                                                         | 不允许                                               |
| 效率                 | 高                                                           | 低                                                   |
| 迭代器               | Fail-Fast                                                    | Fail-Safe                                            |
| 继承关系             | 继承自 AbstractMap 类                                        | 继承自 Dictionary 类                                 |
| 同步机制             | 不是线程安全的，可通过 Collections.synchronizedMap 方法进行同步 | 是线程安全的，不需要额外的同步操作                   |
| 性能                 | 在多线程环境下，性能好于 Hashtable                           | 在单线程环境下，性能较好，但在多线程环境下性能较差   |
| 迭代顺序             | 不保证键值对的迭代顺序，可能随着时间发生变化                 | 不保证键值对的迭代顺序，可能随着时间发生变化         |
| 初始容量             | 默认为 16                                                    | 默认为 11                                            |
| 扩容因子             | 默认为 0.75                                                  | 不可调整                                             |
| 使用场景             | 单线程环境下，推荐使用 HashMap                               | 多线程环境下或需要线程安全的场景，推荐使用 Hashtable |

HashTable还有一个子类**Properties**，也是常见的Map实现类

Properties主要应用于从xxx.properties文件中，加载数据到Properties类对象并进行修改等操作

比如，假设有一个名为 `config.properties` 的属性文件，内容如下：

```properties
# Database Configuration
db.url=jdbc:mysql://localhost:3306/mydb
db.username=myuser
db.password=mypassword

# Logging Configuration
log.level=DEBUG
log.filepath=/path/to/logs
```

我们则可以使用properties对象来读取这些信息:

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;

public class ConfigReader {
    public static void main(String[] args) {
        Properties properties = new Properties();
        try (FileInputStream fileInputStream = new FileInputStream("config.properties")) {
            properties.load(fileInputStream);

            // 读取数据库配置信息
            String dbUrl = properties.getProperty("db.url");
            String dbUsername = properties.getProperty("db.username");
            String dbPassword = properties.getProperty("db.password");
            System.out.println("Database URL: " + dbUrl);
            System.out.println("Database Username: " + dbUsername);
            System.out.println("Database Password: " + dbPassword);

            // 读取日志配置信息
            String logLevel = properties.getProperty("log.level");
            String logFilePath = properties.getProperty("log.filepath");
            System.out.println("Log Level: " + logLevel);
            System.out.println("Log File Path: " + logFilePath);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

在应用上有些类似go中通过os.getenv方法去读取.env文件中的信息

## Collections工具类

Collections工具类中提供了一系列封装好的便于我们操作Set、List、Map等集合的对象的静态方法，比如排序、查询、修改等操作

以下的是Collections类中常见的20个方法:

| 方法签名                                                     | 描述                                           |
| :----------------------------------------------------------- | :--------------------------------------------- |
| `static <T> void sort(List<T> list)`                         | 对列表进行升序排序                             |
| `static <T> void reverse(List<T> list)`                      | 反转列表中的元素                               |
| `static <T> void shuffle(List<T> list)`                      | 随机打乱列表中的元素                           |
| `static <T> int binarySearch(List<? extends Comparable<? super T>> list, T key)` | 使用二分查找算法在有序列表中查找指定元素的索引 |
| `static <T> T max(Collection<? extends T> coll)`             | 返回集合中的最大元素                           |
| `static <T> T min(Collection<? extends T> coll)`             | 返回集合中的最小元素                           |
| `static <T> boolean addAll(Collection<? super T> c, T... elements)` | 将指定元素添加到集合中                         |
| `static <T> int frequency(Collection<?> c, Object o)`        | 返回指定元素在集合中出现的次数                 |
| `static <T> boolean replaceAll(List<T> list, T oldVal, T newVal)` | 将列表中的所有旧值替换为新值                   |
| `static <T> void copy(List<? super T> dest, List<? extends T> src)` | 将源列表中的元素复制到目标列表                 |
| `static <T> void fill(List<? super T> list, T obj)`          | 用指定对象填充列表的所有元素                   |
| `static <T> boolean disjoint(Collection<?> c1, Collection<?> c2)` | 检查两个集合是否没有公共元素                   |
| `static <T> Collection<T> synchronizedCollection(Collection<T> c)` | 返回指定集合的线程安全封装版本                 |
| `static <T> List<T> synchronizedList(List<T> list)`          | 返回指定列表的线程安全封装版本                 |
| `static <K,V> Map<K,V> synchronizedMap(Map<K,V> m)`          | 返回指定映射表的线程安全封装版本               |
| `static <T> Set<T> synchronizedSet(Set<T> s)`                | 返回指定集合的线程安全封装版本                 |
| `static <T> Collection<T> unmodifiableCollection(Collection<? extends T> c)` | 返回指定集合的不可修改视图                     |
| `static <T> List<T> unmodifiableList(List<? extends T> list)` | 返回指定列表的不可修改视图                     |
| `static <K,V> Map<K,V> unmodifiableMap(Map<? extends K,? extends V> m)` | 返回指定映射表的不可修改视图                   |
| `static <T> Set<T> unmodifiableSet(Set<? extends T> s)`      | 返回指定集合的不可修改视图                     |

```java
public static void main(String[] args) {
    List list =  new ArrayList();
    list.add("1");
    list.add("2");
    list.add("3");
    list.add("4");

    Collections.reverse(list);
    System.out.println(list); // [4, 3, 2, 1]
    Collections.shuffle(list);
    System.out.println(list); // [2, 3, 4, 1]
    Collections.sort(list);
    System.out.println(list); // [1, 2, 3, 4]
}
```

## 
