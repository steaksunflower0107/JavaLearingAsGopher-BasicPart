# 异常处理

由于程序遭遇异常(Exception)后会在将其抛出之后会中断运行，但有些运行时错误的等级并不像SOF和OOM一样是JVM无法处理的错误(Error)而是可以接受的，在被处理过后并不会影响到服务的主体功能，那么任由程序因这样的错误而崩溃显然是无法满足一个服务对于稳定性的要求，故为了提高程序的健壮性，语言设计者们大多提供了异常处理机制，使我们可以**在某个代码区域**内手动去**捕捉**异常并在捕捉成功后进入中断，立马进入可自定义的异常处理的逻辑，处理完成后，在中断处恢复程序

java中并没有go中`defer`这样的关键字显式的将通过`recover()`完成的异常捕捉的逻辑压入到函数栈中，它提供的是类似Python中的异常处理机制，将我们认为可能会出现异常的代码块通过`try`关键字包起来，并在其后通过`catch()`方法的参数列表中选定可能会出现的异常类型，在方法体内编写相应异常类型的处理逻辑

比如，除0异常:

```java
public static void main(String[] args) {
    int num1 = 1;
    int num2 = 0;
		
    try {
        int res = num1 / num2;
    } catch (Exception e) {
        e.printStackTrace(); // 追溯发生异常的函数栈
    }

    System.out.println("still running...");
}
```

输出:

```java
java.lang.ArithmeticException: / by zero
	at javaReview.ExecptionHandle.ExceptionHandle.main(ExceptionHandle.java:9)
still running...
```

## 异常体系图
<img width="785" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/7e12c980-97ed-4608-bc32-deb1d99e3f31">

上图表述了各异常之间的继承关系，可见会引起程序崩溃的事件可分为:

- 异常

  - 运行时异常(可自定义是否处理以及如何处理)

    - 数字格式异常
    - 空指针异常
    - 数学运算异常
    - 数组越界异常
    - 类型转换异常
    - ...

  - 编译时异常(必须处理，一般发送在和数据库、网络、文件交互等设计IO操作的场景)

    - 找不到文件异常
    - 找不到类异常

    - ...

- 错误(无法被处理的事件，会必然导致程序的崩溃)

  - 栈溢出错误
  - 内存溢出错误
  - ...

![image-20231122150911227](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231122150911227.png)

## 异常操作方式

java中提供了两种异常操作方式，分别是`try-catch-finally`以及`throws`

### try-catch-finally

是可以供用户自行选择(try)去捕捉异常以及如何处理异常(catch)，也可以自行选择是否要加入异常被处理/未被处理后的逻辑(finally)，比如有关**资源释放**的操作

```java
try {
		// 可能出现异常的代码体
} catch(异常) {
		// 处理异常的逻辑
} finally {
  	// 无论异常是否被捕捉到都会执行的逻辑
}
```

比如:

```java
public static void main(String[] args) {
    try {
        String str = "hello!";
        int num = Integer.parseInt(str);
        System.out.println(num);
    } catch (NumberFormatException e) {
        System.out.println(e.getMessage());
    } finally {
        System.out.println("finally is executed anyway");
    }
}
```

输出:

```shell
For input string: "hello!"
finally is executed anyway
```

值得注意的是:

- 在尝试捕捉一块代码中的异常时可以有多个`catch`，用于去捕捉不同类型的异常，但要求父类**异常在后，子类异常在前**，异常在发送时如:

  ```java
  public static void main(String[] args) {
      try {
          String s = null;
          System.out.println(s.toLowerCase());
      } catch (NullPointerException e) {
          System.out.println("NullPointerException");
      } catch (Exception e){
          System.out.println("Exception");
      }
  }
  ```

- `catch`和`finally`都不是在该异常操作方式中必要的元素，对于`try-finnaly`的搭配的应用场景个人认为比较类似于go中的`defer`

### throws

throws关键字顾名思义是一种可供用户**主动**将异常/自定义异常抛出的操作，应用在无法确定某种异常该如何被处理的场景，所有的异常在被抛出时如果在当前方法栈没有捕捉处理对应异常的逻辑，则会再将该异常抛给本方法的调用者，并以此类推，直到异常被委托给JVM处理，程序会崩溃，并在控制台打印异常信息，throws也是java中默认的应对异常的操作方式

比如，我们在某个方法里需要和某个文件进行I/O操作，但我们希望在该方法中只专注于交互逻辑的实现，此时我们就可以把可能发送的异常抛出，交给上游的方法去捕捉处理

```java
class ThrowsExample {
    public void communicateWithAFile() throws FileNotFoundException {
        FileInputStream fis = new FileInputStream("home/a.txt");
    }
}
```

当然，抛出的异常也可以是我们认为可能发送的异常的父类，比如上述代码可以改写为:

```java
class ThrowsExample {
    public void communicateWithAFile() throws Exception {
        FileInputStream fis = new FileInputStream("home/a.txt");
    }
}
```

也可以抛出多个异常:

```java
class ThrowsExample {
    public void communicateWithAFile() throws FileNotFoundException, NullPointerException {
        FileInputStream fis = new FileInputStream("home/a.txt");
    }
}
```

值得注意的是:

当子类重写的某个方法抛出了异常时，**子类方法也必须要抛出该异常或者抛出该异常的子类**

```java
class Base {
    public void method() throws RuntimeException {
        
    }
}

class Sub extends Base {
    @Override
    public void method() throws NullPointerException {
        System.out.println("method has been override");
    }
}
```

#### throw

java中还提供了一种抛出异常的关键字`throw`，应用的场景是在编码中手动生成一个异常对象，且这个对象往往是我们自定义的异常类的实例

以下是throw和throws的对比信息:

|          | `throw`                                                | `throws`                                                     |
| -------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 用法     | 用于抛出异常对象                                       | 用于声明方法可能抛出的异常类型                               |
| 作用     | 抛出指定的异常对象，中断当前方法的执行                 | 声明方法可能抛出的异常，通知调用者需要处理或传递异常         |
| 使用位置 | 方法内部                                               | 方法声明处                                                   |
| 异常处理 | 被抛出的异常需要在方法内部进行捕获和处理               | 调用该方法的代码需要进行异常处理或继续向上层调用者传递异常   |
| 多异常   | 可以使用多个 `throw` 语句抛出多个异常                  | 可以使用逗号分隔多个异常类型来声明方法可能抛出的多个异常     |
| 异常继承 | 可以抛出任何类型的异常，包括受检异常和非受检异常       | 只能声明方法可能抛出的受检异常，不包括非受检异常             |
| 异常检查 | 编译器不会对抛出的异常进行检查，可以抛出任何类型的异常 | 编译器会对方法可能抛出的受检异常进行检查，要求调用者进行异常处理或继续传递异常 |
| 异常传递 | 异常在方法内部抛出后，可以直接传递到上层调用者         | 声明方法可能抛出的异常后，调用者必须进行相应的异常处理或继续传递异常 |

## 自定义异常类

如果Exception及其子类都不能满足我们对需要抛出的异常的信息在描述上的匹配度，那么我们可以自定义异常，即编写一个名称可自定义的异常类，其需要遵循以下规则:

自定义异常类必须要继承Exception或者RuntimeException，选择的依据是:

- 如果认为该自定义异常属于编译异常，则应继承Exception
- 如果认为该自定义异常属于运行时异常，则应继承RuntimeException，这也是一般自定义异常所继承的

比如，我们可以自定义一个年龄异常:

```java
public class ExceptionHandle {
    public static void main(String[] args) {
        int age = 130;
        if(age < 0 || age >= 120) {
            throw new AgeException("your age is invalid");
        }
    }
}

class AgeException extends RuntimeException {
    public AgeException(String message) {
        super(message);
    }
}
```

输出:

```shell
Exception in thread "main" javaReview.ExecptionHandle.AgeException: your age is invalid
	at javaReview.ExecptionHandle.ExceptionHandle.main(ExceptionHandle.java:11)
```

