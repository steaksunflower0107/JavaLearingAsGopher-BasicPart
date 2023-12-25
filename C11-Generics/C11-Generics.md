# 泛型

以go语言圣经在接口这一章节的第一句话:"接口是约定"的句式概况泛型的用途-**泛型是约束**，通过泛型机制我们可以对集合中的元素的类型、类中属性类型、方法中的参数类型等进行约定，以此来在**编译层面**解决一些在数据转换时易发生异常和强转时效率大幅降低的问题，在集合的应用上可供我们可以放心的对集合中的元素执行某个类型所特有的操作，减小了程序因强转不成而崩溃的概率

通过在`<>`符号内写入被约束的类型(单列、双列集合的参数数量不一样)，即应用了泛型的约束作用，如创建一个只允许存储Integer类型的ArrayList:

```java
ArrayList<Integer> arrayList = new ArrayList<Integer>();
```

其实泛型在不被显式指明时，语言在原生层面上也应用了泛型，如我们创建一个支持任意类型的ArrayList:

```java
ArrayList arrayList = new ArrayList();
```

是等价于:

```java
ArrayList<Object> arrayList = new ArrayList<Object>();
```

泛型的应用还有一点事提高了在开发时的舒适度，应用泛型限制了集合中的数据类型，ide就不会划黄线报错了，我们也因此不必使用@SuppressWarnings去抑制警告

至此，我们发现如果仅以起到**约束**作用的泛型应用场景，即在对象的应用层面去理解泛型，似乎与"泛"字在含义上相背而驰，不能达到见文知意的理念

如果你也是一名gopher，可以思考一下，**go语言中是否支持泛型？**

你可能认为，go中也是支持泛型的，因为go中在创建引用类型时可以直接进行数据类型的约束，比如:

```go
m := make(map[string]string)
```

在类似上类似java中的:

```java
Map<String, String> m = new HashMap<String, String>();
```

我们发现**在对象的应用层面**，两者似乎表现的是同一概念，但事实上**go在1.18版本之前并不支持泛型**，这是因为理解泛型的关键点是"泛"，即"anything"

以下的例子或许可以帮助你在对象应用层面之外，即在编译层面上去理解泛型，泛型也可以应用在类和方法中，以表示**支持任何被约束的类型**，这也被称为**自定义泛型**

```java
class Person<T> {
    T name;
    
    public Person(T name) {
        this.name = name;
    }
    
    public T method() {
        return this.name;
    }
}
```

**T即是泛型**，这里可以使用任意的字母去标识，但T是java官方所推荐的规范写法

我们可以自由去约束**T在该类中代表什么类型**:

```java
public static void main(String[] args) {
    Person<String> person = new Person<String>("ryan"); 
    System.out.println(person.method()); // ryan
    Person<Integer> person1 = new Person<Integer>(123);
    System.out.println(person1.method()); // 123
    Person<Character> person2 = new Person<Character>('a');
    System.out.println(person2.method()); // a
}
```

在go1.18后，泛型也被支持，如:

```go
func Swap[T any](a, b T) (T, T) {
		return b, a
}

func main() {
    a, b := 10, 20
    fmt.Println("交换前：", a, b)
    a, b = Swap(a, b)
    fmt.Println("交换后：", a, b)

    x, y := "Hello", "World"
    fmt.Println("交换前：", x, y)
    x, y = Swap(x, y)
    fmt.Println("交换后：", x, y)
}
```

## 泛型的应用实例

除了上述篇幅已经介绍的在集合中去约束的数据类型的作用外，还可以在类中去提供被约束的数据类型，即自定义泛型，泛型的使用数量是不限的:

```java
class 类名<T, K, V...>
```

T,K,V...等泛型可以在**属性和方法**中使用，表示在创建对象时传入的对应类型，类似匹配符(严格按照参数列表的顺序)

既然在方法和属性中支持泛型，那么同理在接口中也可以应用泛型:

```java
interface 接口名<T, K, V...>
```

接口中通配符的被约束类型是在接口**被继承或被实现**时所约束的

```java
interface inter<T, K> {
    K get(T t);
    void sayHi(K k);
    default K method(T t) {
        return null;
    }
}

class A implements inter<String, Integer> {
    @Override
    public Integer get(String s) {
        return 123;
    }

    @Override
    public void sayHi(Integer integer) {
        System.out.println(integer);
    }
}

interface inter01 extends inter<String, Integer> {
    void sub();
}

class B implements inter01 {

    @Override
    public Integer get(String s) {
        return null;
    }

    @Override
    public void sayHi(Integer integer) {

    }

    @Override
    public void sub() {

    }
}
```

以上我们在方法中使用了泛型，而泛型方法也支持自定义，语法是:

```java
修饰符 <T, K, V...>  返回类型 方法名(参数列表) {}
```

比如:

```java
class Item {
    // 泛型方法
    public <T, R> void selfIntroduce(T t, R r) {
        System.out.println(t);
        System.out.println(r);
    }
}
```

```java
public static void main(String[] args) {
    Item item = new Item();
    item.selfIntroduce("abc", 123);
    // abc
    // 123
    item.selfIntroduce(true, 'a');
    // true
    // a
}
```

即泛型的应用场景有: **泛型类、泛型接口、泛型方法**

## 泛型使用规则

- 对于`<T, K, V>`中的通配符，只能匹配的是**引用类型**，不支持传入的是基本数据类型

- 对于约束好的类型，依然**支持向上转型**

- 在集合中应用泛型，推荐的写法是不必在运行类型中显式写明约束的类型:

  ```java
  List<String> list = new ArrayList<>();
  ```

- **使用泛型数组时，不能被初始化**，因为在编码时泛型数组的数据类型是未知的，故无法在编译时为其分配空间

- **静态方法不能使用泛型**，静态方法随着类的加载被加载，并在整个生命周期中均不会发生改变，其在被加载时无法确定被约束的数据类型，故无法应用泛型，**在对象被创建时泛型所约束的数据类型才能被JVM所知**

- 泛型方法可以被定义在泛型类中，也可以被定义在普通类中

## 泛型通配符

泛型中具有`<?>`通配符，用于表明支持任意类型传入，即**不被约束**

<?>通配符也可以结合继承关系去使用，使得泛型约束的类型多元化:

- <? extend A> 支持A类型及A类的子类型，约束了泛型的上限
- <? super A> 支持A类型及A类的父类型，约束了泛型的下限

```java
class matchSymbol {
    public void print(List<?> list) {
        for (Object obj : list) {
            System.out.println(obj);
        }
    }
    
    public void printHighEdge(List<? extends C > list) {
        for (C c : list) {
            System.out.println(c);
        }
    }

    public void printLowEdge(List<? super D > list) {
        for (Object d : list) {
            System.out.println(d);
        }
    }
}

class C {}
class D {}
```

## 