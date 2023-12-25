# 抽象类与接口

这一节的篇幅的学习与分享重点是接口，正如go语言圣经在介绍接口的章节的第一节的标题所说，“接口是合约”，**接口**是由于"**父类的不确定性**"而被设计的一种我们不必去实现，但约定了必须实现某个方法的引用数据类型，由此我们可以避免由于父类不确定性的而编码的冗余性，以及不必在子类中去重写冗余的方法

比如，我们知道动物都需觅食，那么按照正常的面向对象思想我们可以创造一个所有动物的父类Animal，并写一个方法eat()，但在编写父类时我们无法确定去继承该类的是什么动物，也就无法确定它会怎样去eat，在类似这种场景下我们就可以选择使用接口去适配

由于接口并不要求我们做任何逻辑上的实现，所以接口单纯的是**具有设计上的价值**，同时也会在传参时减少阅读者的理解时间，在编码上提供了一个**顶级类型**用以**将功能相似但实现逻辑不同的类型整合起来**，这也是**多态**特性的一种体系，因此在设计模式中应用的非常多

## 抽象类

在java中，提供了一个相较于其他语言在**接口实现**上条件更为宽松的类型-抽象类，**类似于一个工作清单**，来允许一个类中有未完成的方法，我们用`abstract`关键字去修饰一个类，则该类变换为一个抽象类

```java
public abstract class Animal {
		String name;
		int age;
		abstract public void eat();
}
```

### 抽象类声明&使用规则

- java中的**抽象类不能被实例化**
- 抽象类不一定需要包含抽象(abstract)方法，但抽象方法一定在抽象类中，即一旦某个方法应被声明成abstract，那么其所在的类就应被声明称abstract
- `abstract`关键字只能修饰类和方法
- 抽象类可以拥有java中一个类可以拥有的任何成员，即抽象类仍具备一个基本类所有的承载能力，比如非抽象的方法、构造器、静态属性等，**抽象类本质上还是一个类**
- 如果一个类继承了抽象类，则它**必须实现抽象类中的所有抽象方法**，除非它将自己也声明为abstract类
- 对于抽象类中的抽象方法，不可以用private、final、static来修饰，否则该方法就无法被实现(继承、重写)，丧失了设计的意义
- 因为抽象类允许有抽象方法，故**当抽象类去实现接口时**，可以**不严格实现接口的所有方法**

比如，我们写一个抽象类`Employee`，让`ProductManager`和`Programmer`去继承它

```java
package javaReview.abstractAndInterface;

abstract public class Employee {
    private String name;
    private int id;
    private double salary;

    public Employee(String name, int id, double salary) {
        this.name = name;
        this.id = id;
        this.salary = salary;
    }

    // 抽象方法
    public abstract void work();

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }
}
```

```java
package javaReview.abstractAndInterface;

public class ProductManager extends Employee {
    public ProductManager(String name, int id, double salary) {
        super(name, id, salary);
    }

    public void work() {
        System.out.println("根据用户体验和战略为开发创造需求");
    }
}
```

```java
package javaReview.abstractAndInterface;

public class Programmer extends Employee {
    public Programmer(String name, int id, double salary) {
        super(name, id, salary);
    }

    public void work() {
        System.out.println("敲代码");
    }
}
```

抽象类的实践意义在**模版设计模式**中具有良好的体现

## 接口

接口在java中的声明的关键字和go中是相同的，都是`interface`

```java
public interface HealthyMan {
    public void getUpEarly();
    public void GoToBedEarly();
    public void EatHealthyFood();
    public void WorkOutEveryDay();
}
```

但不同于go中在实现接口是隐式的实现，java中当某个类要求实现某个接口时，会通过`implements`关键字去显式的声明

```java
public class Programmer implements HealthyMan {
  
    public void getUpEarly() {
    }

    public void GoToBedEarly() {
    }

    public void EatHealthyFood() {
    }

    public void WorkOutEveryDay() {
    }
}
```

且与go中的接口不同的是，java中的接口**可以有自己的属性，但需要在声明时进行初始化**

```java
public interface HealthyMan {
		// 属性
		public String motto = "live healthy!";
		// 方法
    public void getUpEarly();
    public void GoToBedEarly();
    public void EatHealthyFood();
    public void WorkOutEveryDay();
}
```

接口中的所有方法都是**抽象方法**，因为我们不必在接口中实现它，但在interface中我们并**不需要用abstract显式修饰**

java中的接口还有一点与go中不同，在JDK8以后java中接口**支持写入非抽象的方法，即可以用自己的方法体**，但必须要用`default`关键字声明，可以理解为该方法的一个默认模板

```java
public interface HealthyMan {
    // 属性
    public String motto = "live healthy!";
    // 方法
    public void getUpEarly();
    public void GoToBedEarly();
    public void EatHealthyFood();
    public void WorkOutEveryDay();
    
    // 非抽象方法
    default public void sayMotto() {
        System.out.println("live healthy!");
    }
}
```

也可以编写**静态方法**，且不必用`default`修饰

```java
public interface HealthyMan {
    // 属性
    public String motto = "live healthy!";
    // 方法
    public void getUpEarly();
    public void GoToBedEarly();
    public void EatHealthyFood();
    public void WorkOutEveryDay();

    // 非抽象方法
    default public void sayMotto() {
        System.out.println("live healthy!");
    }
		// 静态反复噶
    public static void selfIntroduce() {
        System.out.println("I am a healthy man!");
    }
}
```

### 接口声明&使用规则

- 接口中的所有方法都应是public，由此得以被实现，不过在接口中定义抽象方法时和abstract一样不需要显式的用public修饰，默认即是public

- 一个类可以同时实现**多个**接口

  ```java
  public class Programmer implements HealthyMan, HappyMan{
  }
  ```

- 接口中的属性**必须是**`public static final`的，但不必在声明接口时显式修饰

  也就是说，我们可以通过`接口名.属性名`去访问接口的属性，但不能修改它

  ```java
  public interface HealthyMan {
      // 属性
      public String motto = "live healthy!";
      // 方法
      public void getUpEarly();
      public void GoToBedEarly();
      public void EatHealthyFood();
      public void WorkOutEveryDay();
  
      // 非抽象方法
      default public void sayMotto() {
          System.out.println("live healthy!");
      }
  
      public static void selfIntroduce() {
          System.out.println("I am a healthy man!");
      }
  }
  ```

  ```java
  public static void main(String[] args) {
    	// 访问接口属性
      System.out.println(HealthyMan.motto);
  }
  ```

  

- 接口中的属性必须要在**声明时就初始化**

  访问接口的属性有三种方法:

  - 接口名.属性名
  - 实现接口的类名.属性名
  - 实现接口的类的对象名.属性名

  那么当某个类继承了某个类同时实现了某个接口，但父类和接口中有名称相同的属性，我们如何访问到它们呢？

  ```java
  interface A {
      int a = 1;
      void implementA();
  }
  
  class B {
      int a = 2;
  }
  
  class C extends B implements A {
  
      public void implementA() {
      		// 会输出什么？
          System.out.println(a);
      }
  }
  ```

  这样的编码是通过不了编译的，ide会提示:

 <img width="756" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/56b7a3dc-207e-4622-a0c2-45b99cb46700">


  解决的方法是，由于接口中的所有属性都默认被修饰为`public static fianl`，故在此场景我们可以通过`接口名.属性名`去访问，而对于父类中的属性通过`super.属性名`去访问

  ```java
  class C extends B implements A {
  
      public void implementA() {
          System.out.println(A.a);
          System.out.println(super.a);
      }
  }
  ```

- 接口在声明时不能继承(extends)其他类，但可以继承其他多个接口，在继承其他接口后，实现该接口的类就也需要实现其父接口中的方法

- 接口在访问权限上，只能由`public`和`默认`去修饰

### 接口多态

对于所有实现了某个接口的类，接口成为了它们的上层类型，由此也就拥有了多态特性，即**接口类型的引用可以指向实现该接口的类的实例**

```java
public static void main(String[] args) {
    HealthyMan ryan = new Programmer();
}
```

```java
public interface HealthyMan {
    // 属性
    public String motto = "live healthy!";
    // 方法
    public void getUpEarly();
    public void GoToBedEarly();
    public void EatHealthyFood();
    public void WorkOutEveryDay();

    // 非抽象方法
    default public void sayMotto() {
        System.out.println("live healthy!");
    }

    public static void selfIntroduce() {
        System.out.println("I am a healthy man!");
    }
}
```

```java
public class Programmer implements HealthyMan {
    public static void main(String[] args) {
        System.out.println(HealthyMan.motto);
    }
    public void getUpEarly() {
    }

    public void GoToBedEarly() {

    }

    public void EatHealthyFood() {

    }

    public void WorkOutEveryDay() {

    }

    public void sayMotto() {
        HealthyMan.super.sayMotto();
    }
}
```

#### 多态传递

上面的篇幅有介绍说，接口可以继承另一个接口，而多态中上下层关系即是依靠继承所实现的，在接口之间的继承依然有同样的效果

```java
public class interfaceExtends {
    A a = new verify();
    B b = new verify();
}


interface A {
    void implementA();
}

interface B extends A {
  	// 接口A是接口B的上层
    void implementB();
}

class verify implements A, B {
    public void implementA() {

    }

    public void implementB() {
        
    }
}
```

可以看到，当一个类实现了某个接口，还可以使得该接口的**父接口的引用去指向它的对象**，这也是一种向上转型

#### 浅谈接口的实际意义

设计出接口的意义除了在接口这一篇幅开篇时所讲到的在设计上的意义以及作为一个顶级类型的集合去作为其下级类型(实现接口的类)的容器外(即多态特性)，还有对java中单继承机制的能力补充，即我们可以通过接口来赋予一个类更多的能力

我们知道支持面向对象的语言都不支持多继承，是为了避免**菱形问题**，从而和继承的设计概念"is-a"发生冲突，之所以有面向对象的编程思想，是用以支持开发者在实现业务逻辑时能更贴合实际生活中的场景

我们就以继承这一词举一个简单例子，如果你“继承”了你的母亲(女同志A)又继承了另一个母亲(女同志B)，这是不合理的，因为只有你的真正的母亲的基因才会和你的基因中有"is-a"这种关系，不可能再有另外一个女同志能比你的母亲在基因的匹配度上更高，你**天生**就会拥有一些你的母亲所有的能力、天赋

但上述场景如果代入到实际生活中在逻辑上不能自洽，你的基因上虽然和你的母亲的基因有"is-a"的关系，但并不意味着随着时间流逝你只会拥有你母亲的能力，比如女同志A虽然生下来了你，但在你的成长过程中大部分时间是女同志B照顾和指导你，你通过**后天的学习**学会了女同志B的一些能力，不过你的母亲依然只能是女同志A，这是无法改变的，但为了能更全面的展现你的能力，在这种场景下就可以通过接口来通过"like-a"的关系

以下是编码实现这个例子:

女同志A:

```java
public class MissA {
    String Gene = "x";

    public void jump() {
        System.out.println("I can jump very high");
    }
}
```

女同志B:

```java
public interface MissB {
    String ability = "code very well";
    void coding();
}
```

你:

```java
public class You extends MissA implements MissB {
    public void showGene() {
        System.out.println(super.Gene);
    }

    public void showMyTalent() {
        super.jump();
    }

    public void coding() {
        System.out.println(MissB.ability);
    }
}
```

因此，接口可以赋予类类似"后天"学习的能力，从而一定程度上补充了单继承机制，由此对于一个类的能力的描述可以更饱满和自由，甚至在JDK8之后接口支持写入非抽象方法后，还可以提高的代码的复用性

同时接口也类似继承在某个类实现接口时区分出了上下层关系来实现了多态特性，因此接口在一定程序上的实际意义也和继承有所重合
