# 枚举类和注解

## 自定义类实现枚举

枚举类是应用在我们已知**某个类型其所有的可能情况**时，且这些情况是只读的，我们可以在类中就将其情况通过创建**静态final**对象的方式枚举出来，方便调用者使用

比如，四季的情况是我们已知的，我们可以创建一个枚举类Season

```java
public class Enumeration {
    public static void main(String[] args) {
        System.out.println(Season.SPRING); // spring is warm
    }
}


class Season {
    private String name;
    private String desc;

    public static final Season SPRING = new Season("spring", "warm");
    public static final Season SUMMER = new Season("summer", "hot");
    public static final Season AUTUMN = new Season("autumn", "cool");
    public static final Season WINTER = new Season("winter", "cold");

    private Season(String name, String desc) {
        this.desc = desc;
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public String getDesc() {
        return desc;
    }

    public String toString() {
        return this.name + " is " + this.desc;
    }
}
```

根据枚举类的应用场景和编写特性，可见其有以下特点:

- **构造器是私有的**，且不需要对属性提供setXxx方法，因为枚举的对象通常是**只读**的
- 枚举的对象/属性通常用`final static`修饰，且要对外暴露(public)，因为要枚举的情况通常是已知的，如此可以提高执行效率

## Enum类

java中提供了`enum`关键字去声明一个**枚举类**，我们使用`enum`声明的类来替换上述的自定义枚举类:

```java
public class Enumeration {
    public static void main(String[] args) {
        System.out.println(Season.SPRING); // spring is warm
    }
}

enum Season {
    SPRING("spring", "warm"),
    SUMMER("summer","hot"),
    AUTUMN("autumn", "cool"),
    WINTER("winter", "cold");

    private String name;
    private String desc;
    
    private Season(String name, String desc) {
        this.desc = desc;
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public String getDesc() {
        return desc;
    }

    public String toString() {
        return this.name + " is " + this.desc;
    }
}
```

可见，enum类中并不需要显式的通过创建对象/属性的方式去列举所有情况，从而简化了代码量，其还具有以下特点:

- 声明的枚举情况(对象/属性)**必须写到类体的最前列**，且在不同的枚举情况之间，用`,`分割，当枚举完了再用`;`结尾

- 所有编写的enum会默认继承lang包中的**Enum**类，且会被默认修饰为一个**final类**

- 如果使用的是**无参构造器**去创建常量对象则`()`可以被省略

  ```java
  enum Season {
      SPRING("spring", "warm"),
      SUMMER("summer","hot"),
      AUTUMN("autumn", "cool"),
      WINTER("winter", "cold"),
    	// Unknown等同于Unknown(), 即调用的是无参构造器
      Unknown;
  
      private String name;
      private String desc;
  
    	// 无参构造器
      private Season() {
          
      }
      private Season(String name, String desc) {
          this.desc = desc;
          this.name = name;
      }
  
      public String getName() {
          return name;
      }
  
      public String getDesc() {
          return desc;
      }
  
      public String toString() {
          return this.name + " is " + this.desc;
      }
  }
  ```

  也就意味着，下述的enum类是可以通过编译的，因为无参构造器是被默认声明的

  ```java
  enum Gender {
  		MELE, FEMELE;
  }
  ```

- enum声明的类**本质仍是一个类**，也可以进行实现接口等操作

## Enum类常见方法

### name()

输出调用该方法的枚举对象在编码定义时的名称

```java
public class Enum {
    public static void main(String[] args) {
        System.out.println(Season.SPRING.name()); // SPRING
    }
}
```

### ordinal()

输出枚举对象的次序编号

```java
public class Enum {
    public static void main(String[] args) {
        System.out.println(Season.SPRING.ordinal()); // 0
        System.out.println(Season.SUMMER.ordinal()); // 1
        System.out.println(Season.AUTUMN.ordinal()); // 2
        System.out.println(Season.WINTER.ordinal()); // 3
    }
}

enum Season {
    SPRING("spring", "warm"),
    SUMMER("summer","hot"),
    AUTUMN("autumn", "cool"),
    WINTER("winter", "cold"),
    Unknown;

    private String name;
    private String desc;

    private Season() {

    }
    private Season(String name, String desc) {
        this.desc = desc;
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public String getDesc() {
        return desc;
    }

    public String toString() {
        return this.name + " is " + this.desc;
    }
}
```

### values()

返回包含当前枚举中所有对象的**数组**

```java
public static void main(String[] args) {
    Season[] values = Season.values();
    for (Season s : values) {
        System.out.println(s);
    }
}
```

输出:

```shell
spring is warm
summer is hot
autumn is cool
winter is cold
null is null
```

### valueOf()

输入对象名称，若能在枚举类中找寻到相应名称的对象，则返回该对象，若找不到则会抛出一个异常

```java
public static void main(String[] args) {
    Season spring = Season.valueOf("SPRING");
    System.out.println(spring); // spring is warm
}
```

### compareTo()

返回两个枚举类中的对象的**次序编号**的差

```java
public static void main(String[] args) {
    System.out.println(Season.SUMMER.compareTo(Season.SPRING)); // 1 - 0 = 0
}
```

## 注解

注解(Annotation)也被称为元数据(Metadata)，用于修饰包、类、方法、属性、构造器、局部变量等

注解是java中提供的一种和注释一样并不会影响程序的运行逻辑的对代码的说明信息，但是不同于注释的是注解是**可以被编译或运行**的

注解在java SE中的使用场景比较单一，主要是用于标记，而在**java EE**中注解是很重要的角色，例如用来配置应用程序的切面等

使用注解需要在其包含的信息前加`@`符号，并将其当做一个修饰符一样去使用

### 常见注解

java中有三个基本注解

#### @Override

**只能修饰方法**，用于表面该方法是一个重写父类方法的方法

写和不写还是有区别的，注明是Override后JVM会在执行到该方法时去**检查是否真的重写了父类的中的方法**，若未通过检查，则**无法通过编译**

```java
class Base {
    public void overrideMe() {

    }
}

class Sub extends Base {
    @Override
    public void overrideMe() {
        System.out.println("method has been override");
    }
}
```

#### @Deprecated

可以修饰方法、类、属性、包等，用于表面某个元素**已经过时**，主要应用在代码新旧版本的过渡中

但被修饰的元素，**依然可以被正常使用**，故该注解只是**指向性的作用**

```java
@Deprecated
class Base {
    public void overrideMe() {

    }
}
```

#### @SuppressWarnings

常修饰于方法、类，用于抑制方法、类中的警告信息，就像是在go中，使用`_`不用判断err了一样

```java
public class Annotation {
  	// 在{}内写入需要抑制哪些类型的警告
    @SuppressWarnings({"rawtypes", "unused"})
    public static void main(String[] args) {
        List list = new ArrayList();
    }
}
```

以下是部分抑制警告信息及其说明的汇总:

| Option                   | Description                                              |
| :----------------------- | :------------------------------------------------------- |
| all                      | 抑制所有警告                                             |
| boxing                   | 抑制装箱、拆箱操作时的警告                               |
| cast                     | 抑制映射相关的警告                                       |
| dep-ann                  | 抑制启用注释的警告                                       |
| deprecation              | 抑制过期方法警告                                         |
| fallthrough              | 抑制在switch语句中缺失break的警告                        |
| finally                  | 抑制finally模块没有返回的警告                            |
| hiding                   | 抑制隐藏变量的警告                                       |
| incomplete-switch        | 忽略没有完整的switch语句                                 |
| nls                      | 忽略非nls格式的字符                                      |
| null                     | 忽略对null的操作                                         |
| rawtypes                 | 使用generics时忽略没有指定相应的类型                     |
| restriction              | 抑制使用不推荐或禁止的引用警告                           |
| serial                   | 忽略在serializable类中没有声明serialVersionUID变量的警告 |
| static-access            | 抑制不正确的静态访问方式警告                             |
| synthetic-access         | 抑制子类没有按最优方法访问内部类的警告                   |
| unchecked                | 抑制没有进行类型检查操作的警告                           |
| unqualified-field-access | 抑制没有权限访问的域的警告                               |
| unused                   | 抑制未使用的代码的警告                                   |

其作用域和其注明的位置相关，比如注明在方法前，则作用域是在该方法中，放在类前就是作用于整个类中

## 元注解

元注解是**修饰注解的注解**，在JDK的源码中有很多应用，有助于为阅读源码的读者提供更多的信息

大致有以下几种:

- Retention

  注明注解的作用范围(保留多长时间)，有三种值

  - RetentionPoliy.SOURCE : 注解只在编译期内保留，编译完成后注解会被丢弃(最短的)，比如@Override注解
  - RetentionPoliy.CLASS: 编译器会将注解记录在.class文件中，但当java程序运行时，JVM不会保留该注解，**这也是注解的Retention的默认值**
  - RetentionPoliy.RUNTIME: 编译器会将注解记录在.class文件中，当java程序运行时，JVM会保留该注解，由此程序可以通过反射获取该注解(最长的)

- Target

  注明注解可以修饰哪些元素，比如@Override，它的Target中就只有METHOD(方法)

  比如:

  ```java
  @Target(value = {ElementType.CONSTRUCTOR, ElementType.FIELD, ElementType.LOCAL_VARIABLE, ElementType.METHOD, ElementType.PACKAGE,
  ElementType.PARAMETER, ElementType.TYPE})
  ```

- Documented

  注明注解是否会在javadoc中保留

- Inherited

  若父类中的注解的被Inherited修饰，则其子类中会自动继承该注解

比如，在JDK源码中`@Deprecated`注解就被以上的大部分元数据有所修饰

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, MODULE, PARAMETER, TYPE})
public @interface Deprecated {
    /**
     * Returns the version in which the annotated element became deprecated.
     * The version string is in the same format and namespace as the value of
     * the {@code @since} javadoc tag. The default value is the empty
     * string.
     *
     * @return the version string
     * @since 9
     */
    String since() default "";

    /**
     * Indicates whether the annotated element is subject to removal in a
     * future version. The default value is {@code false}.
     *
     * @return whether the element is subject to removal
     * @since 9
     */
    boolean forRemoval() default false;
}
```

## 