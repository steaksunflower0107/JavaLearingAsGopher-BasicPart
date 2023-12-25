# 类与对象

直观的把java中的类与对象和go中做一个类比:

类(class) -> 结构体(struct)

属性(attributes) -> 字段(field)

举个在代码中的直观类比

java:

```java
class Cat {
    String name, color;
    int age;
}
```

go:

```go
type Cat struct {
	name, color string
	age         int
}
```

实例化(创建对象)

java:

```java
public class javaReview {
    public static void main(String[] args) {
        Cat cat = new Cat();
        cat.age = 2;
        cat.color = "white";
        cat.name = "puppy";
    }
}
```

go:

```go
func main() {
    var cat Cat
    cat.age = 2
    cat.color = "white"
    cat.name = "puppy"
}
```

这里这样写是为了和java能更好的对比，而go中一般的实例化的写法是这样的:

```go
func main() {
    cat := &Cat{
      name:  "puppy",
      color: "white",
      age:   2,
    }
}
```

由于java中没有赋予用户使用指针的能力，所以在操作对象的时候，和go中有一些不同:

- go语言中，struct是一个值类型，但在操作大对象时，根据内存逃逸分析，我们往往会选择操作结构体指针，以减小GC的压力
- 而在java中，我们可以直接将对象就理解为一个结构体指针，因此我们类比说java中的class，不同于go中的struct的是，它是一个引用类型，相对go语言java中省去是否要去选择操作结构体还是结构体指针这个过程

在java中，属性在和类进行绑定时就会**进行初始化**，未被赋值的属性会被赋予其所属数据类型的零值，这和在方法中单独声明变量时是不同的，这点和go语言十分相似

```java
Cat cat = new Cat();
cat.color = "white";
cat.name = "puppy";

System.out.println(cat.age); // 0
```

## 对象在JVM的内存中存在形式

这里先有一个大概的了解，后续的篇幅中会有较为深入的学习，大致的重点是:

对于jvm的内存结构大致的分配机制是

- 栈: 一般存放基本数据类型(局部变量)
- 堆: 存放对象
- 方法区: 常量池(常量，比如字符串)、类加载信息

在对象创建的流程中有

- 实例化的对象存放在jvm中的栈区
- 类的属性根据数据类型的不同，来决定存放的是值还是地址
  - 若属性为引用类型，则以地址的形式存放在堆区
  - 若属性为基本数据类型，则直接存放的是值
- 堆中存放的地址指向的是在方法区中的常量池中实际存放的值
- 在实例化的过程中，jvm会把类的信息(属性、方法)加载到方法区

在调用方法时

- 当执行到一个方法时，会开辟一个独立的栈空间，即方法的所需的内存分配在栈上

## 方法

这里还是先通过一个例子和go语言做类比，声明一个Person类(结构体)，有两个属性(字段)，有一个speak方法(绑定方法)

java:

```java
class Person {
    String name;
    int age;

    public void speak() {
        System.out.println("have a good day!");
    }
}
```

go:

```go
type Person struct {
	name string
	age  int
}

func (p *Person) Speak() {
	fmt.Println("have a good day!")
}
```

不同于go的是，java是一门纯面向对象的语言，在为某个类写方法的时候，方法的方法体会写在class中，我个人认为这样在代码的可读性上相对于go要更好，更加直观，可以轻松看到某个类具有哪些方法，而在go中如果没有刻意的在代码中给不同结构体的方法排个序，可能不够直观

java成员方法的定义是:

```java
访问修饰符 返回参数的数据类型 方法名(参数列表) {
		方法体;
		return 返回值;
}
```

和go中函数的写法中所需要的部分基本是一致的，除了访问修饰符的部分，

### 访问修饰符

用于控制**方法和属性以及类(部分)**的访问权限

- public: 任何类都可以访问
- protected: 仅子类和在同一个包中的类可以访问
- 默认(即不写): 仅同一个包中的类可以访问
- private: 只有类本身的方法可以访问

|           | 类内部 | 本包 | 子类 | 外部包 |
| --------- | ------ | ---- | ---- | ------ |
| public    | √      | √    | √    | √      |
| protected | √      | √    | √    | ×      |
| default   | √      | √    | ×    | ×      |
| private   | √      | ×    | ×    | ×      |

访问修饰符的设计是后续很多特定业务场景所遵循的设计模式的基底之一

而在go中，由于并不是一门严格的纯面向对象语言，源码在架构划分上最小的单位是包而不是类，仅有是否将方法名以及字段名大写来控制访问权限

需要注意的是: 当访问修饰符去控制类的访问权限时，只有**默认和public**才可以修饰

### 规则

- 一个方法最多只能有一个返回值，如果希望返回多个值，则可以考虑换成返回的是一个集合类型，这和go是很不一样的，go中支持函数有多个返回值 

- java中方法的返回值可以向下兼容，即精度高的返回值数据类型可以接收精度低的数据类型，如double可以接收int，反之则不可以，因为会发生溢出

  ```java
  public double test() {
      int i;
      i = 2;
      return i;
  }
  ```

  而在go中，这是不可以的，如果要返回和函数声明的返回值类型不同的数据类型，需要手动强转

  ```go
  func test() float64 {
      var i int
      i = 2
      return i // 会报错
  }
  ```

- 在java中由于是纯面向对象，不会有方法在类外部，由此有了调用类内部方法和外部方法上的规则

  - 调用类内部的方法时，通过方法名调用即可
  - 调用外部类中的方法时，先创建一个外部类的实例化对象，通过对象名.方法名调用，调用时需要注意被调用方法的访问修饰符是否允许该方法被调用

### 可变参数

和go中的用法类似，当我们不知道一个方法中要传入多少个**同数据类型**的参数时，可以采用可变参数的方法来入参

```java
public int sum(int... nums) {
    int ans = 0;
    for (int i = 0; i < nums.length; i++) {
        ans += nums[i];
    }

    return ans;
}
```

需要注意的是:

- 可变参数的实质是一个指定为可变的数据类型的**数组**，即我们也可以直接传入一个同类型的数组，这和go中是一样的

  java:

  ```java
  public class javaReview {
      public static void main(String[] args) {
          VarPara v = new VarPara();
          int[] nums = {1,2,3};
          v.VarPar(nums);
      }
  }
  
  class VarPara {
      public void VarPar(int... nums) {
          System.out.println(Arrays.toString(nums));
      }
  }
  ```

  go:

  ```go
  func main() {
  		VarPar([]int{1, 2, 3}...)
  }
  
  func VarPar(nums ...int) {
  		fmt.Println(nums)
  }
  ```

- 当一个方法中既有普通参数也有可变参数时，需要保证可变参数在形参列表的最后，也与go中的要求一样

  ```java
  public void VarPar(String word, int... nums) {
      System.out.println(Arrays.toString(nums));
  }
  ```

- 根据上一个注意点的要求，即同时要求了**一个方法中只能有一个可变参数**

### 属性与局部变量

在面向对象编程中java在类的使用方式上，有一点和go中实现面向对象编程的结构体是不一样的，java中允许直接给属性(即go中结构体的字段)赋值，且属性的作用域是在整个类中(类比的话就是go中的全局变量)，当然，同go中一样，**在类中方法里的局部变量的优先级大于属性**，也就是就近原则

```java
class Dog {
    int age = 3;

    public void eat(int age) {
        System.out.println(age);
    }
}

public class javaReview {
    public static void main(String[] args) {
        Dog d = new Dog();
        System.out.println(d.age); // 3

        d.eat(6); // 6
    }
}
```

属性的生命周期会往往长于局部变量，这是因为

- 属性会随着对象创建而被创建，在对象被GC回收时而死亡
- 局部变量会随着其方法的调用而创建，随着方法的任务结束(方法栈被回收)而死亡

如果我们给类的属性赋值，则会在类被加载时进行默认初始化，若构造方法中有对对象属性的修改，则会进行显示初始化，覆盖默认初始化的值

## 方法重载

方法重载(Overload)即允许在一个类中，允许多个**同名**方法的存在，但**要求形参列表不一致**

一个很好的的应用例子就是: print

```java
System.out.println(123);
System.out.println("123");
System.out.println(1.23);
System.out.println('s');
```

out是JDK中的PrintStream类中的一个实例化的对象，从使用体验上看，对于同一个方法，我们可以传入不同数据类型的参数，而在源码中可以看到有很多个println方法(形参列表不同)，这里就通过应用方法重载，赋予了方法更多的能力

比如，我们创建一个可以计算int和double类型数据的计算器类，并应用方法重载来整合计算方法的能力

```java
class Calculator {
    public int calculate(int n1, int n2) {
        return n1 + n2;
    }

    public double calculate(double n1, double n2) {
        return n1 + n2;
    }

    public double calculate(double n1, int n2) {
        return n1 + n2;
    }
}
```

对于方法重载，我们需要注意的是:

- 方法名必须与被重载方法**同名**
- 形参列表不能与被重载方法一致

而go语言中是不支持方法重载的，go官方的解释说：**在go的类型系统中，仅通过名称匹配并要求类型的一致性是一个主要的简化决策。**(搬自https://www.zhihu.com/question/40661108)

## 构造方法

构造方法/构造器是为了满足我们在实例化某个类创建对象时就可以完成对于属性的赋值操作，用于简化初始化对象的过程

```java
修饰符 方法名(形参列表) {
		方法体;
}
```

比如我们通过构造方法初始化一个Dog对象:

```java
public class javaReview {
    public static void main(String[] args) {
        Dog d = new Dog("puppy", 3);
    }
}

class Dog {
    int age;
    String name;
    public Dog(String dName, int dAge) {
        name = dName;
        age = dAge;
    }
}
```

而在go中其实是天然支持构造方法，如果我们仅需要实现上述java实现的效果(谁都能创建Dog对象)，我们并不需要为Dog结构体编写一个构造方法

```go
d := &Dog{
  name:  "puppy",
  age:   3,
}
```

但go中并不天然支持**构造方法的访问权限**以及除了给结构体字段赋值以外**其他初始化需要的操作**，因此当我们需要遵从某个设计模式或职责的时候，我们会常常为某个结构体编写一个构造方法，比如很常见的一道面试题LRU缓存的实现:

```go
type LRUCache struct {
	capacity   int
	cache      map[int]*LRUNode
	head, tail *LRUNode
}

type LRUNode struct {
	key, value int
	pre, next  *LRUNode
}

func InitLRUNode(key, value int) *LRUNode {
	return &LRUNode{
		key:   key,
		value: value,
	}
}

// Constructor 构造方法
func Constructor(capacity int) LRUCache {
	cache := make(map[int]*LRUNode)

	res := LRUCache{
		cache:    cache,
		capacity: capacity,
		head:     InitLRUNode(0, 0),
		tail:     InitLRUNode(0, 0),
	}

	res.head.next = res.tail
	res.tail.pre = res.head

	return res
}
```

### 规则

- 构造方法没有返回值，**仅仅是对已经被分配了内存的对象的属性做初始化**

- **构造方法名和类名必须一致**

- 构造方法也可以**重载**，应用在初始化对象时需要传入不同数量属性的场景

- 要把**构造方法和成员方法区分开来**，我们不能去像调用成员方法一样通过对象名.方法名去调用，构造方法的调用是JVM去完成的，当我们编写了它，就赋予了在new对象时直接传属性值的能力

- JVM会为每个类生成一个默认无参的构造方法，可以通过javap反编译指令查看，当用户自行编写了构造方法时，会覆盖默认的构造方法

  ```java
  class Dog {
      int age;
      String name;
  		// 无参构造方法
      public Dog() {
          age = 2;
      }
  }
  ```


## this关键字

this是一个指针，指向的是**当前**对象的内存地址，是**本类**的引用，在java中是显式的，类似于go中绑定方法中的隐式对象指针

java:

```java
class Dog {
    int age;
    String name;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

go:

```go
type Dog struct {
    age int
    name string
}

func (d *Dog) constructor(age int, name string) {
    d.age = age
    d.name = name
}
```

其应用场景有:

- 在**类中的方法**中访问本类的属性、方法、构造方法

  比如，通过this我们可以去创造一个方法用于判断传入的对象和当前对象是否指向同一块内存:

  ```java
  class Dog {
      int age;
      String name;
  
      public Dog(String name, int age) {
          this.name = name;
          this.age = age;
      }
  		// 判断传入的对象和当前对象是否指向同一块内存
      public Boolean compareTo(Dog d) {
          return this.name.equals(d.name) && this.age == d.age;
      }
  }
  ```

- 访问构造方法

  ```java
  this(参数列表)
  ```

  **只能在构造方法中使用，其必须作为构造方法的第一条语句**，顾名思义，只能在构造方法中通过this访问其他的构造方法

## 面向对象特性复习

### 封装

封装(encapsulation) 即把抽象出的数据(属性)和对数据的操作(方法)集合在一起，将数据保护在内部，访问者只有通过被授权(通过访问修饰符控制)的操作才能对数据进行操作

一个很好的解释例子就是: 在华为还没推出标志突破芯片工艺技术封锁时，他们可以拿到支持5G通信的iPhone中的芯片，但却不能即刻知道该芯片内部是如何设计的(数据)，这即是一种封装

**封装的好处**

- 隐藏实现细节，使我们在项目中集成各个类的能力时操作方便
- 保护了类中数据的安全性，我们可以通过访问修饰符与自己定义的判断逻辑去实现

**实现封装**

1. 对类的属性进行私有化，即使用private修饰符

2. 为类提供一个公有的(public)的set方法，用于对实现对可否对属性操作的逻辑判断与实现对属性操作(写)

   ```java
   public void setXxx(类型 参数) {
   		// 验证权限的逻辑
   		this.Xxx = 参数;
   }
   ```

   set方法中覆盖了构造方法能力，因此我们可以将set方法集成到构造方法中去，使得在调用构造方法创建对象时可以利用上封装对于数据权限的检验逻辑，保护数据安全性

   ```java
   public Employee(String name, int age, double salary, String job) {
       this.setName(name);
       this.setAge(age);
       this.setSalary(salary);
       this.setJob(job);
   }
   ```

3. 为类提供一个公有的(public)的get方法，用户获取属性的值(读)

   ```java
   public 访问修饰符 getXxx() {
   		return this.xx;
   }
   ```

如:

```java
class Employee {
    // 需要实现的逻辑:
    /*
        1. 年龄、工资不能直接查看
        2. 年龄的合理范围在[1,120]岁，否则赋默认值18
        3. 姓名的长度在[2,6]
     */
    public String name;
    private int age;
    private double salary;
    private String job;

    public void setName(String name) {
        if (name.length() < 2 || name.length() > 6) {
            return;
        }

        this.name = name;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        // 权限判断的逻辑

        return age;
    }

    public void setAge(int age) {
        if (age < 1 || age > 120) {
            this.age = 18;
            return;
        }

        this.age = age;
    }

    public double getSalary() {
        // 权限判断的逻辑

        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    public String getJob() {
        return job;
    }

    public void setJob(String job) {
        this.job = job;
    }
}
```

而在go中，主要是以方法名、字段名的首字母**是否大写**来实现封装的，以下是一个例子

```go
package model
import "fmt"
type employee struct {
    Name string
    age int   //其它包不能直接访问
    sal float64
}
//写一个工厂模式的函数，相当于构造函数
func NewEmployee(name string) *employee {
    return &employee{
        Name : name,
    }
}

func (e *employee) SetAge(age int) {
    if age < 0 || age > 120 {
        e.age = 18
      	return
    }
  	e.age = age
}
func (e *employee) GetAge() int {
    return e.age
}

func (e *employee) SetSal(sal float64) {
    if sal >= 3000 && sal <= 30000 {
        e.sal = sal
    } else {
        fmt.Println("薪水范围不正确..")
    }
}

func (e *employee) GetSal() float64 {
    return e.sal
}
```

### 继承

继承即某个类可以去**复用**其父类(超类、基类)的**方法和属性**，从而在子类(派生类)中**减少代码冗余**，并使我们在业务实现的设计上更贴合实际生活中的设计思维，是一种**is -a**的关系

```java
class 子类 extends 父类 {
  
}
```

![image-20231113140334670](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231113140334670.png)

**注意点**

- 子类继承的是父类所有的**公开的**属性与方法，如需访问父类私有的属性，则需要通过父类的公共方法(如getXxx方法)去访问

- 子类中的对象再通过**任意**构造方法被创建的时候，会默认**先调用父类的无参构造方法**，完成父类的初始化(这也是一种贴近实际生活的思维，即先有父亲才能有孩子)，再调用子类的构造方法，这是由于JVM会默认在子类的任意构造方法中调用父类的无参构造方法，在代码上相当于在子类的构造方法中隐式的调用了`super()`

  ```java
  class Sub extends Base {
      public Sub() {
      		// 隐式调用，写与不写效果相同
          super();
      }
  }
  ```

- 如果父类没有提供无参构造方法，则必须在子类的构造方法中去用`super(参数列表)`去指定使用父类中的哪个构造器来完成父类的初始化，否则**编译会不通过**

  如果希望指定使用父类中的某个构造方法，也可以直接使用`super(参数列表)`去**显式调用**，且**必须编写在子类构造方法体中的第一行**

  ```java
  class Base {
      String name;
      int age;
  
      public Base(String name, int age) {
          this.name = name;
          this.age = age;
      }
  }
  
  class Sub extends Base {
      public Sub() {
        	// 通过super中传入不同的参数来指定父类中的构造方法
          super("ryan", 18);
      }
  }
  ```

- 当子类的构造方法中使用了`super()`，就不能使用`this()`，这也很好理解，因为两者都要求写在**构造方法体的第一行**

**规则**

- java中所有的类都是Object类的子类，即Object类是所有类的父类(基类、超类)，称为顶级父类
- 子类最多只能继承一个父类，即**java中是单继承的机制**
- 继承不应被**滥用**，在决定是否继承某个类是应先判断是否遵循**is-a**的设计理念，而这个主观判断的思考过程我们可以将其套用在实际生活中去，比如: cat is animal, glass is not animal
- 当子类的属性与父类中的属性重名时，则会**优先在本类中查找值**，若没有再根据继承关系的依次向上查找

#### 浅识在继承关系中的对象内存分布

- 当一个类继承了某个类时，在创建其对象时，会在**方法区**依次从顶级父类开始加载其被继承的类，直到该类的信息也被加载
- **堆**中在分配对象的内存空间时，会根据辈分依次创建好指向其父类的属性以及该类的属性的值的指针
- **栈**中分配指向堆中该对象信息的指针

#### super关键字

super代表队对父类的引用，是用于访问父类的属性、方法、构造方法的指针

和this的用法类似，只不过是指向的父类

但和this在用法上不同的是，super并**不能访问到父类中用priviate修饰符修饰的属性和方法** 

![image-20231113193017493](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231113193017493.png)     

```java
class Base {
    String name;
    int age;

    public Base() {
    }
    
    public void test() {
        System.out.println("this method for test");
    }
}

class Sub extends Base {
    public Sub() {
        super.test();
        System.out.println(super.name + super.age);
    }
  
    public void querySalary() {
        System.out.println(super.salary); // 编译不通过
    }
}
```

**super在实现继承中的应用意义**

- super赋予用户在编写子类的构造方法时，将参数的初始化分工的能力

  ```java
  class Computer {
      String cpu, memory;
      int disk;
      
      public Computer(String cpu, String memory, int disk) {
          this.cpu = cpu;
          this.memory = memory;
          this.disk = disk;
      }
  }
  
  class PC extends Computer {
      private String brand;
      
      public PC(String cpu, String memory, String brand, int disk) {
        	// 父类构造器去初始化父类属性
          super(cpu, memory, disk);
        	// 初始化子类属性
          this.brand = brand;
      }
  }
  ```

- 当子类和父类中的属性、方法出现重名时，super具有区分的意义，使我们得以访问父类中的属性和方法(遵从从年轻到老年的访问顺序)

#### 方法重写

方法重写(覆盖)是继承关系下的概念，若在子类中编写了与父类中**重名**的方法，且**返回类型(有特殊情况)与参数列表**均**相同**，则是方法重写，并且在子类的对象调用该方法时会调用子类重写后的方法，即在子类对象的视角中**覆盖**了父类的方法

**注意点**

- 方法名称与参数列表均相同才是方法重写

- **返回类型并不严格要求相同**，特殊的情况是**子类返回类型是父类返回类型的子类**，如:

  ```java
  class Animal {
      public Object say() {
          return null;
      }
  }
  
  class Bird extends Animal {
      public String say() { // String是Object类的子类
          return null;
      }
  }
  ```

- 子类重写后的方法的访问权限相对于父类中的访问权限不能缩小，比如

  ```java
  protected 父类方法 xxx()  ->  public 子类方法 xxx()  // 正确
  public 父类方法 xxx()  ->  protected 子类方法 xxx()  // 错误
  ```

这里将名称上类似的**方法重载**和**方法重写**作一个对比:

![image-20231113194745249](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231113194745249.png)

可以看到实现方法重写的要求更加严格

接下来我们对比一下在go语言中的继承，go中并不像java中有`extends`关键字去指明继承谁的显式继承，而是通过**字段嵌入**的方式

```go
func main() {
    s := &Sub{
      salary: 3000,
    }

    fmt.Println(s.age) // 0
}

type Base struct {
    name string
    age  int
}

type Sub struct {
  	// 继承父类Base的方法和属性
    Base
    salary int
}
```

- go中虽在继承中并不严格要求有构造方法，这也体现了go语言追求简洁的设计思路

- go中并没有`super`关键字，因为其继承的实现采用的是字段嵌入的方式，因此当我们需要在子类的实例中去调用父类的字段或方法时，采用`子类实例.父类名.父类方法(属性)`即可

- go中虽不支持方法重载，但支持方法重写(覆盖)，其机制类似于java

  ```go
  func main() {
  	m := &Man{}
  	m.Eat()
  	m.Run()
  	m.Sleep()
  }
  
  type Person struct {
  }
  
  func (this *Person) Eat() {
  	fmt.Println("Person Eat")
  }
  
  func (this *Person) Run() {
  	fmt.Println("Person Run")
  }
  
  func (this *Person) Sleep() {
  	fmt.Println("Person Sleep")
  }
  
  type Man struct {
  	Person
  }
  
  func (this *Man) Eat() {
  	// 类似于Java的 super.Eat()
  	this.Person.Eat()
  	fmt.Println("Man Eat")
  }
  
  func (this *Man) Run() {
  	fmt.Println("Man Run")
  }
  ```

  输出:

  ```shell
  Person Eat
  Man Eat
  Man Run
  Person Sleep
  ```

继承丰富了我们在代码设计上的自由度以及减少了代码的冗余，我目前还没有阅读过JDK的源码，但仅就go的SDK和看过的部分k8s的client包的来说，继承是应用十分广泛的(不过go中应用的更多是基于**接口和继承**的**组合**机制)

### 多态

使方法和对象具有**多种状态**，多态的实现是建立在封装和继承基础之上的

对于**状态**一词，我的理解是这样的:

#### 在方法层面

- 方法重载: 通过重载可以赋予方法更多的能力，使其能接收**不同的形参列表**，不同的形参列表会去调用不同的方法体，这就是一种状态上的不同，**方法可以接收不同的形态**

- 方法重写: 通过重写可以赋予方法不同的能力，使其具有不同的**操作权限**(比如private的属性和方法只有本类的方法才可以访问)以及逻辑，比如在如下的例子中:

  ```java
  class Meituan {
      public String companyName;
      private String companyStrategy;
      public String job() {
          return "帮助大家吃的更好，生活的更好";
      }
  
      public String strategyForMedia() {
          // 可以访问到本类的private
          return companyStrategy;
      }
  }
  
  class ProductManager extends Meituan {
      public String job() {
          return "I'm a ProductManager of " + super.companyName + "根据用户体验和战略为开发创造需求";
      }
  }
  
  class Programmer extends Meituan {
      public String job() {
          return "I'm a Programmer of " + super.companyName + "敲代码";
      }
  }
  ```

  我们分别为这三个类创建创建一个对象，并调用`job()`方法

  ```java
  public class javaReview {
      public static void main(String[] args) {
          Meituan m = new Meituan();
          System.out.println(m.job());
  
          ProductManager pm = new ProductManager();
          System.out.println(pm.job());
  
          Programmer p = new Programmer();
          System.out.println(p.job());
      }
  }
  ```

  可见，我们赋予了`job()`方法在隶属于不同类的对象时拥有了不同的状态，这也是面向对象编程去贴合实际生活的导向体现，再比如，每个人都有指纹，但每个人的指纹是不一样，这就是指纹的**多态**性

#### 在对象层面

##### 编译类型&运行类型

编译类型和运行类型是理解和运用多态特性的必备知识

编译类型简而言之，即是在**编译期间**JVM给某个变量分配的**类型**，其在进程/线程/协程运行的整个生命周期中都**不会改变**

而运行类型，顾名思义是指在**运行期间**某个变量的类型，而在运行期间就具有变化性和不确定性，即变量的类型可以**随着进程/线程/协程的运行的运行而改变**，其变化的核心原则是**向上兼容、向下转型**，父类类型可以兼容其子类的类型，而子类的引用若要转为父类的类型则需要强转

在判断向上还是向下转型时，类型的由上到下的梯度关系即继承关系中的由老年到年轻的梯度关系，且**转型的视角是基于对象而言的**，即我们要**从运行类型出发**

如下面两条语句

```java
// Animal是Dog和Cat的父类
Animal a = new Dog(); // 编译类型是Animal，运行类型是Dog
a = new Cat(); // 编译类型仍是Animal，运行类型变为了Cat
```

之所以要区分出这两个概念，我个人的理解是这样的:

- 编译类型**决定**了我们在运行过程中可以给某个变量**集成**的**哪些类**的能力
- 运行类是集成的**某个类**的能力的**封装**

```java
public class ploy {
    public static void main(String[] args) {
        Animal animal = new Dog();
        animal.speak(); // wang~
        animal = new Cat();
        animal.speak(); // miao~
    }
}

class Animal {
    public void speak() {

    }
}

class Cat extends Animal {
    public void speak() {
        System.out.println("miao~");
    }
}

class Dog extends Animal {
    public void speak() {
        System.out.println("wang~");
    }
}
```

比如在上述的代码示例中，我们就为animal这一变量集成了Cat、Dog这两个类的能力的封装，只要我们对**变量变换了其可以接收的运行类型**，变量就具备该运行类型的能力(属性、方法)

在这一点上，与上述的方法层面中的**方法重写部分**所述内容是相互照应的，具体的实现相同，但站在不同的视角(方法、对象)，就对多态有了不同的诠释

多态我认为也**为GC减轻了压力**，因为利用多态特性就可以对同一变量赋予不同的能力，避免了去声明其他类的类型的而创造的变量的内存空间，**提高了java程序的性能**

##### 向下转型

我们先总结一下在**向上转型**中调用方法时的**规则**:

- 对象可以调用父类中所有的成员(受访问修饰符限制)
- **不能调用子类中未重写父类方法的方法**，即子类特有的方法
- 调用方法时查找方法的顺序遵从从子类开始向上查找

其中第二点限制了我们去赋予对象最大化的能力，解决这一点的方法就是**向下转型**

向下转型在实际生活中能映射的例子还是蛮好举例的，比如，把我们自己类比于java中的一个对象(变量)，在我们出生时(程序的生命周期的早期)我们的类型是Person这个父类，有了speak()、run()等能力(方法)，id、gender等属性，即所有人都有的，但在我们的成长过程中(随着程序运行)，我们有了一份职业，我们成为了一名程序员，成了Programmer这个子类的实例化对象，而这个类中coding()这个方法是Person类中没有的，这时候我们就需要**通过向下转型**使得我们coding()的能力也体现出来

简而言之，向下转型就是为了**满足对象可以调用子类类型中所有的成员**

```java
package javaReview.ploy;

public class ploy {
    public static void main(String[] args) {
        Person ryan = new Person("123456", "male", "ryan");
        ryan.run(); // ryan is running!
        ryan.coding(); // 无法调用，编译不通过
        // 后来ryan长大了，有了新的身份programmer
        ryan = new Programmer("123456", "male", "ryan", "Meituan");
      	// 向下转型
        Programmer ryanHasAJob = (Programmer) ryan;
        ryanHasAJob.coding(); // ryan is coding at Meituan
    }
}

class Person {
    String id;
    String gender;
    String name;

    public Person(String id, String gender, String name) {
        this.id = id;
        this.gender = gender;
        this.name = name;
    }

    public void run() {
        System.out.println(this.name + " is running!");
    }

    public void speak() {
        System.out.println(this.name + " is speaking!");
    }
}

class Programmer extends Person {
    String company;

    public Programmer(String id, String gender, String name, String company) {
        super(id, gender, name);
        this.company = company;
    }

    public void coding() {
        System.out.println(this.name + " is coding at " + this.company);
    }

    public void run() {
        System.out.println("A programmer is  running!");
    }

    public void speak() {
        System.out.println("A programmer speaking!");
    }
}
```

**instanceof**

instanceof是一个java中的比较操作符，用于判断对象的`运行类型`**是否属于某个类型或某类型的子类型**

比如:

```java
Person ryan = new Person("123456", "male", "ryan");
// 是否属于某个类型
System.out.println(ryan instanceof Person); // true
```

```java
ryan = new Programmer("123456", "male", "ryan", "Meituan");
Programmer ryanHasAJob = (Programmer) ryan;
// 是否属于某个类型的子类型
System.out.println(ryanHasAJob instanceof Person); // true
```

instanceof可以帮助在强转之前进行判断，以防空指针的出现导致程序崩溃，instanceof在比较保险的编程风格上是很常见的，因为我们也希望赋予对象子类的全部能力

##### 方法动态绑定机制

java中的动态绑定机制是指:

- 当调用对象方法时，方法会和对象的内存地址，即运行类型进行绑定，this指向的是运行类型
- 当调用对象的属性时，没有动态绑定机制，this指向的是当前类型

我们从一个例子中来理解上述对于方法动态绑定的机制

```java
package javaReview.ploy;

public class ploy {
    public static void main(String[] args) {
        Person ryan = new Programmer("123456", "male", "ryan", "Meituan");
        System.out.println(ryan.age);
        ryan.info();
        ryan.birthInfo(); 
    }
}

class Person {
    String id;
    String gender;
    String name;
    int age = 0;

    public Person(String id, String gender, String name) {
        this.id = id;
        this.gender = gender;
        this.name = name;
    }

    public int getAge() {
        return this.age;
    }

    public void info() {
        System.out.println(this.name + " is " + this.getAge() + " years old");
    }

    public void birthInfo() {
        System.out.println(this.name + " is " + this.getAge() + " years old");
    }

}

class Programmer extends Person {
    String company;
    int age = 18;

    public Programmer(String id, String gender, String name, String company) {
        super(id, gender, name);
        this.company = company;
    }

    public int getAge() {
        return this.age;
    }

    public void coding() {
        System.out.println(this.name + " is coding at " + this.company);
    }

    public void info() {
        System.out.println(this.name + " is " + this.getAge() + " years old");
    }
}
```

输出是:

```shell
0
ryan is 18 years old
ryan is 18 years old
```

我们来分析一下结果

对于`ryan`这个变量，其编译类型是`Person`，因此当访问属性时，看`Person`中的属性，age = 0

对于`ryan.info`, 调用的是方法，有自己的查找顺序，在运行类型`Programmer`中发现重写了`Person`类中的`info`方法，故只关注`Programmer`中的`info`方法即可，其调用了`this.getAge`，即本类返回年龄的方法，故age输出的是18

对于`ryan.birthInfo`, 调用的是方法，有自己的查找顺序，在运行类型`Programmer`中发现并没有此方法，而`Person`中有，故只关注`Person`中的`birthInfo`方法即可，其调用了`this.getAge`，**可返回的为什么是子类`Programmer`中的age？**

这里就是方法动态绑定机制的体现，即**对象的内存和其运行类型绑定在了一起**，故`this`关键字指向的是其**运行类型**，无论要访问的方法在什么类中才被查找到，只要方法体中调用的方法有被运行类型的类中**重写**过，调用的都是运行类型中的方法

##### 延伸讨论

现在我们可以思考为什么说多态是基于继承和封装实现的呢？

我个人的理解是，多态的核心是**转型**和**能力赋予**

而转型避免不了继承，更具体的说是**避免不了方法重写**，且多态的本质就是:

**父类的引用指向了子类的对象**

能力赋予以对应着封装，我们将能力(属性、方法)集成在了一个类中，需要注意的是，这里可以赋予的方法**只能是重写过的**

而对于当使用对象的属性，对应是对象的编译类型中的属性，即**访问属性看编译类型**，在**访问方法时，才有查找顺序**

##### 应用: 多态数组

通过多态，我们可以声明一个拥有若干子类的**父类类型的数组**，由此创建了一个可以存贮了不同类型的容器(不过所有的类型都是数组的声明类型的子类型或者其本身)，本质上是利用了**向上转型**的特性

在访问到数组中的某个元素时，JVM会去判断它当前的运行类型，并赋予其运行类型中封装的能力

父类Person:

```java
package javaReview.ployArray;

public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String say() {
        return name + "\t" + age;
    }
}
```

子类: Student

```java
package javaReview.ployArray;

public class Student extends Person {
    private double score;

    public Student(String name, int age, double score) {
        super(name, age);
        this.score = score;
    }

    public double getScore() {
        return score;
    }

    public void setScore(double score) {
        this.score = score;
    }

    public String say() {
        return super.say() + " score=" + this.score;
    }
  
    public void study() {
        System.out.println(this.getName() + " is studying");
    }
}
```

子类: Teacher

```java
package javaReview.ployArray;

public class Teacher extends Person {
    private double salary;

    public Teacher(String name, int age, double salary) {
        super(name, age);
        this.salary = salary;
    }

    public String say() {
        return super.say() + " salary=" + this.salary;
    }
  
  	public void teach() {
        System.out.println(this.getName() + " is teaching");
    }
}
```

多态数组实现:

```java
package javaReview.ployArray;

public class PloyArray {
    public static void main(String[] args) {
        Person[] persons = new Person[3];

        persons[0] = new Person("ryan", 18);
        persons[1] = new Student("steak", 22, 98);
        persons[2] = new Teacher("vuri", 29, 20000);

        for (Person person:persons) {
            System.out.println(person.say());
          	// 向下转型，使得对象也具有子类所有的能力
            if (person instanceof Student) {
                Student student = (Student) person;
                student.study();
            } else if (person instanceof Teacher) {
                Teacher teacher = (Teacher) person;
                teacher.teach();
            }
        }
    }
}
```

输出:

```shell
ryan	18
steak	22 score=98.0
steak is studying
vuri	29 salary=20000.0
vuri is teaching
```

常见的应用还有动态参数等，本质还是向上转型兼容，就不列出实例

## Object类常用方法

这里篇幅列出一些常常会被子类重写的Object类中的方法

首先是equals()方法

```java
public boolean equals(Object obj)
```

常常会被重写，用于赋予更贴合的比较逻辑，在之前运算符的篇幅中有所描述

### hashCode()方法

```java
public int hashCode()
```

用于提高诸如HashMap、HashTable等具有**哈希结构**的容器的效率

hashCode()方法保证了在程序的生命周期中同一对象调用该方法返回的值是相等的，**前提是不修改对象上的`equals()`方法比较中使用的信息**

由于java程序是跑在JVM中的，我们无法获取java中某个对象在本计算机上实际内存逻辑地址，而hashCode()返回的值，我们可以在比较是否是同一个对象时把它当做是内存地址一样去使用，在逻辑上可以认为该值是由JVM从实际内存逻辑地址上映射而来的

```java
Person ryan = new Person("ryan", 18);
Person Ryan = ryan;
System.out.println(ryan.hashCode());
System.out.println(Ryan.hashCode());
```

输出:

```java
757108857
757108857
```

在集合中，改方法也往往会被重写，后续会在集合的篇幅中讨论

### toString()方法

```java
public String toString() {
  	// getClass().getName()即类的全类名(包名 + 类名)
    // Integer.toHexString(hashCode())将对象的hashCode转换为16进制
  	return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

改方法的意义是返回对象的属性信息，子类中重写该方法的意义也是如此

仅目的上有些类似go中gorm框架中的`TableName`方法，返回表名信息用于在数据库链接时绑定

比如，我们重写toString()方法输出Person类中对象的信息

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

如果我们在控制台输出一个对象，则在输出时**会默认调用toString方法**

```java
Person ryan = new Person("ryan", 18);
System.out.println(ryan);  // 等价于System.out.println(ryan.toString());
```

### finalize()方法

```java
@Deprecated(since="9") protected void finalize() throws Throwable
```

这在java中的GC中会涉及到的方法，java的GC采用的策略是引用计数，当对象的没有任何引用时，JVM会自动调用该对象的finalize方法回收它的内存空间

JDK11 API文档对该方法的定义是:

```apl
当Java finalize虚拟机确定不再有任何方法可以被任何尚未死亡的线程访问时，它被调用，除非是因为最终确定其他一些准备完成的对象或类所采取的行动。 finalize方法可以采取任何操作，包括使该对象再次可用于其他线程; 然而， finalize的通常目的是在对象被不可撤销地丢弃之前执行清理操作。 例如，表示输入/输出连接的对象的finalize方法可能会执行显式I / O事务，以在永久丢弃对象之前断开连接。
```

在之后的学习过程或开发过程中如果有遇到重写finalize()方法的案例和实际情况，我再基于个人的理解延伸这部分

## 类方法和类变量

### 类变量

也称为静态(static)变量

该概念的出现的主要目的是去适配当**同一个类的多个对象需要对同一块内存进行操作**时的场景

类变量(属性)即在同一个类中的对象**共享**的变量，**在类加载时就会被初始化**，并伴随类的整个生命周期，而对于该类变量的存储区域：

- 在jdk8前，一般认为是存储在方法区的静态域中
- 在jdk8后，一般认为是存储堆中

当需要将一个类中的属性转换为类变量时，需要加上`static`关键字

#### 访问类变量

根据规范比较推荐的访问方式是`类名.类变量名`，虽然通过对象名.类变量名也可以访问到，但前者在语义上更能突出是**一个类共享**的变量，而后者的语义是某个对象独有的属性

由此不难得出，即使没有为一个类去实例化任何对象，仍然可以访问到它，这也印证了类变量的初始化是在类被加载时就会进行的

需要注意的是，类变量的访问仍然严格遵照访问修饰符的权限规则

### 类方法

同类变量的用法类似，加载机制和类变量一样都是**在类加载时初始化**(类中的普通方法也是如此，也同样都加载到方法区)，都是需要通过`static`关键字去将一个普通方法转为类方法(静态方法)

类方法的设计初衷并不是像类变量一样去满足某个特定的场景，而是单纯的去**提高程序的执行效率**

其应用的场景是:

当类中的一个方法体中**不涉及到任何和对象相关的成员**(和this、super有关的)，我们可以将其为转为静态方法，比如工具类中的方法、打印操作、排序等等一些**通用**的方法，比如JDK中的Math类就有大量类方法(粘贴了部分如下)

```java
public final class Math {

    /**
     * Don't let anyone instantiate this class.
     */
    private Math() {}

    /**
     * The {@code double} value that is closer than any other to
     * <i>e</i>, the base of the natural logarithms.
     */
    public static final double E = 2.7182818284590452354;

    /**
     * The {@code double} value that is closer than any other to
     * <i>pi</i>, the ratio of the circumference of a circle to its
     * diameter.
     */
    public static final double PI = 3.14159265358979323846;

    /**
     * Constant by which to multiply an angular value in degrees to obtain an
     * angular value in radians.
     */
    private static final double DEGREES_TO_RADIANS = 0.017453292519943295;

    /**
     * Constant by which to multiply an angular value in radians to obtain an
     * angular value in degrees.
     */
    private static final double RADIANS_TO_DEGREES = 57.29577951308232;

    /**
     * Returns the trigonometric sine of an angle.  Special cases:
     * <ul><li>If the argument is NaN or an infinity, then the
     * result is NaN.
     * <li>If the argument is zero, then the result is a zero with the
     * same sign as the argument.</ul>
     *
     * <p>The computed result must be within 1 ulp of the exact result.
     * Results must be semi-monotonic.
     *
     * @param   a   an angle, in radians.
     * @return  the sine of the argument.
     */
    @HotSpotIntrinsicCandidate
    public static double sin(double a) {
        return StrictMath.sin(a); // default impl. delegates to StrictMath
    }

    /**
     * Returns the trigonometric cosine of an angle. Special cases:
     * <ul><li>If the argument is NaN or an infinity, then the
     * result is NaN.</ul>
     *
     * <p>The computed result must be within 1 ulp of the exact result.
     * Results must be semi-monotonic.
     *
     * @param   a   an angle, in radians.
     * @return  the cosine of the argument.
     */
    @HotSpotIntrinsicCandidate
    public static double cos(double a) {
        return StrictMath.cos(a); // default impl. delegates to StrictMath
    }
}
```



```java
class Tools {
    public static double calSum(double n1, double n2) {
        return n1 + n2;
    }
}
```

在访问的规范性上，和类变量相同

需要注意的是，在编写类方法时，除了自定义的一些**通用**逻辑外，只能访问的类的静态变量和静态方法

#### 理解main方法

java程序的入口是

```java
public static void main(String[] args) {}
```

我们来根据其构成来分析为什么是这样编写的:

- 为什么是public? 

  在运行一个java程序时，JVM首先调用的就是main方法，故改方法的权限需要是public

- 为什么是static? 

  JVM在调用main方法时并不需要创建任何对象，也不需要返回给JVM任何值或者地址，且一个java进程main方法的生命周期即进程的生命周期，故使用了static和void

  且正因为**main方法是一个静态方法**，意味着我们在main方法中就可以**在不创建对象的情况下访问本类中的静态方法和静态变量**

- 为什么传入的参数是String[]? 

  我们先遍历一下传入的字符串数组

  ```java
  public class MainMethod {
      public static void main(String[] args) {
          for (String s : args) {
              System.out.println(s);
          }
      }
  }
  ```

  输出:

  ```shell
  
  ```

  输出是空的

  但如何我们通过命令行的方式去运行它，并附上几个参数

  ```shell
  java MainMethod main method ryan
  ```

  输出:

  ```shell
  main method ryan
  ```

  可见，`args`接收的是命令行参数，用法类似于go中的os.Args，不过go和go的第三方库中还提供了很多包来更好的支持特定场景的命令行参数输入，比如flag、cobra，这在容器化部署的时候非常常见，而在java中，main方法天然支持这一点

## 代码块

代码块(初始化块)被设计创造的意义是**减少代码的冗余**，比如在一个类中如果多个构造器中有相同的逻辑，我们可以将其抽出来写到代码块中，其会在**对象创造时被执行**

我们可以将其理解为一种另一形式的构造器，在类被加载或对象被创建时**完成一些初始化相关的操作**

它类似于方法，是类的一部分，将初始化的逻辑语句封装到方法体中，即是一个代码块

```java
[修饰符] {
		逻辑;
  	...
}
```

其有一些特点:

- 在实例化一个对象时，代码块的**调用优先级高于构造器**

- 代码块和和方法一样可以在方法体编写逻辑语句，但其**没有方法名、返回值、参数列表**

- 代码块的调用是**隐式调用**，在类加载时或对象被创建时即会被调用

- 代码块可以被访问修饰符修饰，但只能写static，即代码块可以被分为static代码块和普通代码块

- static代码块和普通代码块的区别是:

  - static代码块的作用是完成一些**类**的初始化工作，它随类的加载而被执行，且**只会执行一次**
  - 普通代码在**对象创建**时，就会被执行

  这里我们再温习一下**类什么时候会被加载**:

  1. 创建对象实例时(new)
  2. 当一个类的子类的对象被创建时，改类也会被加载
  3. 使用类的静态成员时(类变量、类方法)

### 浅识类的加载过程

至此，我们可以剖析一下在一个对象被创建时，在**一个**类中的加载过程

1. **调用**静态代码块和**完成**静态变量的**初始化**，若有多个静态代码块和静态变量，则按照**编码时的顺序**进行初始化或调用
2. 调用普通代码块和完成普通变量的初始化，若有多个普通代码块和普通变量则顺序同1
3. 调用构造方法

```java
class ClassLoadOrder {
    public static void main(String[] args) {
        A a = new A();
    }
}

class A {
    private static int n = getN();
    private int m = getM();

    static {
        System.out.println("静态代码块被调用");
    }

    {
        System.out.println("普通代码块被调用");
    }
  
    public static int getN() {
        System.out.println("n被初始化");
        return 100;
    }

    public static int getM() {
        System.out.println("m被初始化");
        return 10;
    }

    public A() {
        System.out.println("构造方法被调用");
    }
}
```

输出:

```shell
n被初始化
静态代码块被调用
m被初始化
普通代码块被调用
构造方法被调用
```

即按照了编码时的顺序进行初始化和调用

如果一个类继承了某个类，那么类的加载过程会变为:

1. 调用**父类的**静态代码块和完成**父类的**静态变量的初始化，若有多个静态代码块和静态变量，则按照**编码时的顺序**进行初始化或调用
2. 调用**本类的**静态代码块和完成**本类的**静态变量的初始化，若有多个静态代码块和静态变量，则按照**编码时的顺序**进行初始化或调用
3. 调用**父类**的普通代码块和完成**父类**的普通变量的初始化，若有多个普通代码块和普通变量则顺序同1
4. 调用**父类**的构造方法
5. 调用**本类**的普通代码块和完成**本类**的普通变量的初始化，若有多个普通代码块和普通变量则顺序同2
6. 调用**本类**的构造方法

简而言之，**先父后子，先静态后普通&构造方法**，关于类加载的详细过程会在后续的关于JVM的部分中学习到

## final关键字

final关键字用于去修饰一些我们在整个进程/线程/协程的生命周期中都不希望其值、地址发生变化的局部变量、属性、方法

- final也可以修饰类，则该**类不能再被任何类继承**，断子绝孙，比如Integer、Double、Float、Boolean、String都是被final修饰了的类

  ```java
  final class 类名 {
  }
  ```

- 对于方法，当被final修饰，则该方法**不能再被子类重写/覆盖**，但可以被继承的子类使用，类似于遗嘱

  ```java
  public final void hi(){
  }
  ```

  且如果一个类已经被final修饰了，则没有必要再对该类中的方法用final修饰

  注意: **final不能修饰构造方法**

- 对于属性，当被final修饰，则该属性的**引用地址不能再被修改**，转换为一个常量属性，适用于诸如税率、圆周率这样具有**公共性**且不常修改的值

  ```java
  class salary {
  		public final double TAX_RATE = 0.08;
  }
  ```

  final和static往往搭配起来使用，因为编译器在底层上对这样的搭配做了优化处理，由此可以提升程序的执行效率

- 对于局部变量，当被final修饰，则该变量**转换为一个局部常量**，在java中的常量，命名规范是**全大写**并采用蛇形命名法

  ```java
  class Person {
  		public int calculateSalaryAfterTax() {
  				final TAX_RATE = 0.08;
  				...
  		}
  }
  ```

- final在参数列表时也可以使用，但注意不能对被final修饰的传入的参数有修改操作

对于常量的声明与赋值，我们可以:

- 在类的属性中通过`final`关键字声明常量，后**在构造器或代码块中赋值**
- 在类的属性中通过`final`关键字**声明常量时就赋值**
- 如果`final`修饰的是一个静态变量，则只能在声明时或在静态代码块中赋值，**不能在构造器中赋值**

## 内部类

内部类顾名思义是嵌入在某个类中的类

```java
class Outer {  // 外部类
		class Inner {  // 内部类
				
		}
}
```

内部类根据编码的位置不同，又可分为局部内部类(有类名)、匿名内部类(无类名)、成员内部类(未用static修饰)、静态内部类(使用static修饰)

### 局部内部类

局部内部类是编写在外部类的局部位置上(比如方法、代码块中)，且具有自己的类名

它具有以下机制:

- 可以**直接访问**外部类的所有成员，**包括私有的**，因为`private`的可访问范围是本类中的成员

- **不能用访问修饰符修饰**，因为局部内部类在类中的角色地位**等同于一个局部变量**，但是和局部变量一样可以使用final修饰，不过相对于final修饰局部变量是使其成为了一个常量，局部内部类用final修饰是声明了其不允许被继承

  它在角色地位上等同于一个局部变量也意味着，**外部其他类是不可以访问到局部内部类的**

  ```java
  class OuterClass {
      private int n = 100;
      private void method1() {}
      
      private void method2() {
          class Inner {
              public void getOuterNAndMethod1() {
                	// 可以访问外部类中私有的成员
                  System.out.println(n);
                  method1();
              }
              
          }
      }
  }
  ```

- 外部类访问局部内部类的成员**需要通过在方法内创建实例化的对象**才能进行访问

  ```java
  package javaReview.abstractAndInterface;
  
  public class InnerClass {
      public static void main(String[] args) {
          OuterClass outer = new OuterClass();
          outer.method2();
      }
  }
  
  class OuterClass {
      private int n = 100;
      private void method1() {}
  
      public void method2() {
          class Inner {
              public void getOuterNAndMethod1() {
                  System.out.println(n);
                  method1();
              }
          }
  				// 创建内部类对象
          Inner inner = new Inner();
        	// 访问内部类成员
          inner.getOuterNAndMethod1();
      }
  }
  ```

- 作用域仅限于包含其的方法或代码块中

- 访问成员时遵循就近原则，若想访问外部类中的成员可以使用`外部类名.this.成员名`去访问

  ```java
  class OuterClass {
      private int n = 100;
      private void method1() {}
  
      public void method2() {
          class Inner {
              int n = 10;
              public void getOuterN() {
                  System.out.println(n); // 10
                	// 外部类名.this.成员名
                  System.out.println(OuterClass.this.n); // 100
                  method1();
              }
          }
  
          Inner inner = new Inner();
          inner.getOuterN();
      }
  }
  ```

  这里关于`this`的用法也再次印证了this在含义上指向的是当前对象

### 匿名内部类

和局部内部类一样，匿名内部类是定义在外部类中的中的局部位置，其特点顾名思义是没有名称

还有一个显著特点是匿名内部类会在被加载时即刻返回一个**对象**，我们能操作的也只有它的对象，它的应用场景类似go中通过`go`关键字起一个goroutine，用于去执行一个**一次性**的任务，而java中是通过创建一个**只能被实例化一次**的类，并在创建完成后**立即返回其实例**

匿名内部类是唯一一种没有构造器的类，一般来说，匿名内部类用于继承其他类或是实现接口，并不需要增加额外的方法，只是对继承方法的实现或是重写。

```java
// 声明匿名内部类
new 类或接口(参数列表) {
		类体
};
```

#### 实现接口

我们可以通过匿名内部类在方法中实现了一个接口，从而避免了在外部再写一个类，提高了编码效率与降低内存开销

```java
class OuterClass {
    public void method1() {
      	// 匿名内部类
        Inter inter = new Inter() {
            public void implementsMe() {
                System.out.println("Inter has been implemented");
            }
        };
        inter.implementsMe();   // Inter has been implemented
    }
}

interface Inter {
    void implementsMe();
}
```

值得注意的是，对于`Inter inter = new Inter()`这一条语句，根据之前对于编译类型和运行类型的判断经验，我们可能会认为对于`inter`这个对象它的编译、运行类型都是`Inter`，其实不然，这里是JVM在底层就默认应用了多态的特性，它的编译类型是`Inter`，但运行类型正式我们所创造的**匿名内部类**，在底层的实现上，仍然有我们熟知的一般的实现接口的操作

```java
class XXX(匿名的) implement Inter {
    public void implementsMe() {
    		System.out.println("Inter has been implemented");
  	}
}
```

在java中，我们可以通过Object类的`getClass()`方法去查看某个对象的运行类型，通过该方法查看`inter`的运行类型:

```java
public class InnerClass {
    public static void main(String[] args) {
        OuterClass outer = new OuterClass();
        outer.method1();
    }
}

class OuterClass {
    public void method1() {
        Inter inter = new Inter() {
            public void implementsMe() {
                System.out.println("Inter has been implemented");
            }
        };
        inter.implementsMe();   // Inter has been implemented
        System.out.println(inter.getClass());
    }
}

interface Inter {
    void implementsMe();
}
```

输出:

```java
Inter has been implemented
class javaReview.abstractAndInterface.OuterClass$1
```

可见匿名内部类在JVM中实际上是还有**名称**的，供JVM去寻址调度，匿名内部类会用`类名+$+序号`去标识

#### 继承类

匿名内部类还可以用于在编码时简化编写一个类的子类

```java
class OuterClass {
    public void method1() {
        FatherClass son = new FatherClass() {
          	// 重写父类中的方法
            public void running() {
                System.out.println("Son is running");
            }
        };
    }
}

class FatherClass {
    public void running() {
        System.out.println("Father is running");
    }
}
```

匿名内部类在**访问权限机制**上**和局部内部类完全相同**，因为他们都是定义在类中的局部位置上

#### 最佳实践

匿名内部类的机制和多态特性是绑定的，因此我们可以利用其在传入的参数是只需要被实例化一次的类的对象时完成简洁高效的**参数传递**

```java
public class InnerClass {
    public static void main(String[] args) {
      	// 传入匿名内部类的对象
        method1(new Inter() {
            public void show() {
                System.out.println("Method show has been implemented");
            }
        });
    }

    // 形参是接口类型的方法
    public static void method1(Inter inter) {
        inter.show(); // Method show has been implemented
    }
}

interface Inter {
    void show();
}

```

### 成员内部类

成员内部类定义在外部类的成员位置，并且不能用`static`修饰，提供了在类中整合类能力的能力

```java
public class InnerClass {
    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.UseInner();
    }
}

class Outer {
    private int n = 100;
    public String name = "Outer";

  	// 成员内部类
    class Inner {
        public void getN() {
            System.out.println(n);
        }
    }

    public void UseInner() {
      	// 实例化成员内部类
        Inner inner = new Inner();
        inner.getN();
    }
}
```

它具备以下机制:

- 可以直接访问外部类中的**所有**成员

- 可以添加任意的访问修饰符，它的地位和类中的其他四种成员(属性、方法、代码块、构造方法)一样

- 作用域是整个类体

- 和外部类中的成员的访问机制是:

  - 成员内部类访问外部类**直接访问**即可

  - 外部类访问成员内部类需要**先创建对象才能访问**

    ```java
    public void UseInner() {
        // 实例化成员内部类
        Inner inner = new Inner();
        inner.getN();
    }
    ```

- 在**外部其他类**中，访问成员内部类的方式有两种:

  - 先创建外部类对象，通过外部类对象创建成员内部类对象

    ```java
    public class InnerClass {
        public static void main(String[] args) {
        		// 创建外部类对象
            Outer outer = new Outer();
            // 通过外部类对象创建成员内部类对象
            Outer.Inner inner = outer.new Inner();
        }
    }
    
    class Outer {
        private int n = 100;
        public String name = "Outer";
    
        public class Inner {
            public void getN() {
                System.out.println(n);
            }
        }
    
        public void UseInner() {
            Inner inner = new Inner();
            inner.getN();
        }
    }
    ```

  - 外部类暴露返回成员内部类对象的方法

    ```java
    public class InnerClass {
        public static void main(String[] args) {
            Outer outer = new Outer();
            Outer.Inner inner = outer.getInner();
        }
    }
    
    class Outer {
        private int n = 100;
        public String name = "Outer";
    
        public class Inner {
            public void getN() {
                System.out.println(n);
            }
        }
    		// 返回成员内部类对象的方法
        public Inner getInner() {
            return new Inner();
        }
    }
    ```

### 静态内部类

和成员内部类一样，编写在外部类的成员位置，区别是使用`static`去修饰

```java
class Outer {
    private int n = 100;
    private static int m = 10;
    public String name = "Outer";
		
  	// 静态内部类
    static class Inner {
        public void getNAndM() {
            System.out.println(n); // 访问不到
            System.out.println(m); // 可以访问
        }
    }
}
```

它具有的机制是:

- 可以直接访问到外部类中所有的**静态成员**，不能访问非静态成员

  ```java
  static class Inner {
      public void getNAndM() {
          System.out.println(n); // 访问不到
          System.out.println(m); // 可以访问
      }
  }
  ```

- 可以用任何访问修饰符修饰，作用域是整个类体

- 在和外部类的访问方式上和成员内部类是相同的

- **外部其他类**访问静态内部类有两种方式

  - 由于是静态的，可以通过`外部类名.内部类名`直接创建对象访问

    ```java
    public class InnerClass {
        public static void main(String[] args) {
          	// 外部类名.内部类名
            Outer.Inner inner = new Outer.Inner();
        }
    }
    
    class Outer {
        private int n = 100;
        private static int m = 10;
        public String name = "Outer";
    
        static class Inner {
            public void getM() {
                System.out.println(m);
            }
        }
    }
    ```

  - 外部类暴露返回静态内部类对象的方法

    ```java
    public class InnerClass {
        public static void main(String[] args) {
            Outer outer = new Outer();
            Outer.Inner inner = outer.getInner();
        }
    }
    
    class Outer {
        private int n = 100;
        private static int m = 10;
        public String name = "Outer";
    
        static class Inner {
            public void getNAndM() {
                System.out.println(m); // 可以访问
            }
        }
        
      	// 暴露返回静态内部类对象的方法
        public Inner getInner() {
            return new Outer.Inner();
        }
    }
    ```

  - 当外部类成员和静态内部类成员重名时，在静态内部类中访问依然遵循就近原则，但在访问外部类的成员时，由于只能访问静态成员，可以直接通过`外部类名.成员名`去访问，而不是`外部类名.this.成员名`

至此，类中的**五种成员**我们已经初步学习完毕，分别是**属性、方法、构造方法、代码块、内部类**
