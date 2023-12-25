# 转义字符

转义字符似乎在各语言中都大差不差

1. /t 制表符，四个空格
2. /n 换行符
3. \\\ 一个\
4. \\" 一个"
5. \\' 一个'
6. \r 一个回车，并将光标定义到当前行行首

# 注释

- 单行注释: 与go完全一样 //

- 多行注释

  ```java
  /*
  	注释内容
  */
  ```

- 文档注释

  它以 **/\**** 开始，以 ***/** 结束。

  文档注释允许你在程序中嵌入关于程序的信息。

  你可以使用 javadoc 工具软件来生成信息，并输出到 HTML 文件中。

  ```java
  /**
   * @author ryan
   * @version 0.0.1
   */
  ```

更多文档注释的标签:

| @author       | 标识一个类的作者                                       | @author description                                          |
| ------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| @deprecated   | 指名一个过期的类或成员                                 | @deprecated description                                      |
| {@docRoot}    | 指明当前文档根目录的路径                               | Directory Path                                               |
| @exception    | 标志一个类抛出的异常                                   | @exception exception-name explanation                        |
| {@inheritDoc} | 从直接父类继承的注释                                   | Inherits a comment from the immediate surperclass.           |
| {@link}       | 插入一个到另一个主题的链接                             | {@link name text}                                            |
| {@linkplain}  | 插入一个到另一个主题的链接，但是该链接显示纯文本字体   | Inserts an in-line link to another topic.                    |
| @param        | 说明一个方法的参数                                     | @param parameter-name explanation                            |
| @return       | 说明返回值类型                                         | @return explanation                                          |
| @see          | 指定一个到另一个主题的链接                             | @see anchor                                                  |
| @serial       | 说明一个序列化属性                                     | @serial description                                          |
| @serialData   | 说明通过writeObject( ) 和 writeExternal( )方法写的数据 | @serialData description                                      |
| @serialField  | 说明一个ObjectStreamField组件                          | @serialField name type description                           |
| @since        | 标记当引入一个特定的变化时                             | @since release                                               |
| @throws       | 和 @exception标签一样.                                 | The @throws tag has the same meaning as the @exception tag.  |
| {@value}      | 显示常量的值，该常量必须是static属性。                 | Displays the value of a constant, which must be a static field. |
| @version      | 指定类的版本                                           | @version info                                                |

## Javadoc工具

javadoc工具将你的java程序的源代码作为输入，输出一些包含你程序注释的HTML文件。

每一个类的信息将在独自的HTML文件里。javadoc 也可以输出继承的树形结构和索引。

由于 javadoc 的实现不同，工作也可能不同，你需要检查你的 Java 开发系统的版本等细节，选择合适的 Javadoc 版本。

使用格式

```shell
javadoc -d 路径 -注释1 -注释2 文件名
```

**实例**

```java
import java.io.*;
 
/**
* 这个类演示了文档注释
* @author Ayan Amhed
* @version 1.2
*/
public class SquareNum {
   /**
   * This method returns the square of num.
   * This is a multiline description. You can use
   * as many lines as you like.
   * @param num The value to be squared.
   * @return num squared.
   */
   public double square(double num) {
      return num * num;
   }
   /**
   * This method inputs a number from the user.
   * @return The value input as a double.
   * @exception IOException On input error.
   * @see IOException
   */
   public double getNumber() throws IOException {
      InputStreamReader isr = new InputStreamReader(System.in);
      BufferedReader inData = new BufferedReader(isr);
      String str;
      str = inData.readLine();
      return (new Double(str)).doubleValue();
   }
   /**
   * This method demonstrates square().
   * @param args Unused.
   * @return Nothing.
   * @exception IOException On input error.
   * @see IOException
   */
   public static void main(String args[]) throws IOException
   {
      SquareNum ob = new SquareNum();
      double val;
      System.out.println("Enter value to be squared: ");
      val = ob.getNumber();
      val = ob.square(val);
      System.out.println("Squared value is " + val);
   }
}
```

在经过 javadoc 处理之后，SquareNum 类的注释将在 SquareNum.html 中找到。

# 变量

## 变量声明

两种方式:

- 先声明再赋值

  ```java
  int a;
  a = 10;
  ```

- 声明时赋值

  ```java
  int a = 10;
  ```

需要注意的是，如果你采用了第一种方式，则需要初始化，这是和go语言中有很大不同的地方

go中如果声明了一个变量，就已经被赋予了其类型的零值，除了一些引用类型，比如切片、map、chan，需要去make去初始化

```go
var a int
fmt.Println(a) // 0
```

而java中不一样

```java
int holiday;
System.out.println(holiday); // 会报错 “可能尚未初始化”
```

### Java10的新特性

从java10开始，对于局部变量，如果可以从变量的初始值判断出它的类型，就不再需要声明类型，只需要使用关键字var而无须指定类型

```java
var holiday = 10;
```

变量声明上和go是比较相似的，除了go独有的:=

# 数据类型

分为两大类：基本数据类型、引用数据类型

## 基本数据类型

### 数值型

- 整数类型，存放整数
  - byte(1个字节), short(2个字节), int(4个字节), long(8个字节)

![image-20231101185601894](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231101185601894.png)

- 浮点数类型，存放小数
  - float(4个字节), double(8个字节)

![image-20231101191232556](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231101191232556.png)

**一个字节是8位有符号数，能表示的数据范围是-128~127**

**float在小数点后7位之后的数据就会被舍弃掉了**

和go语言类比一下的话:(左go右java)

​    int32 -> int

​    int64 -> long

go中还有较java独特的uint系列，即无符号的整型

​    float32 -> float

​    float64 -> double

但是在go中，如果我们给一个浮点类型的变量赋予了一个整数的值，那么在打印后，console输出的也是一个整数值，但在java中不同，**对于浮点类型在控制台的输出一定是一个小数**，如是整数则会自动添加小数点并在小数点后加0

#### 使用规则

**整型**

1. java中的整型的范围与长度不受不同os的影响
2. java中的整型常量**默认是****int，若声明的是long类型的常量需**在值后加"l"或"L"**，**long类型最好以大写L来添加尾缀，因为小写l容易和数字1混淆。** 

```java
// 会报错的情况
int a = 127l;  // 因为由8字节转为4字节，会有精度丢失
// 正确情况
long a = 127l;
```

**浮点型**

1. 关于浮点数在机器中存放的形式是: **浮点数 = 符号位 + 指数位 + 尾数位**
2. 尾数部分可能会丢失，造成精度损失
3. java中的浮点型常量**默认是double**，是java官方所建议的，若声明的是float型常量需**在值后加"f"或"F"**，'
4. 特殊的赋值方式

```java
// 四种声明方式
  double num1 = 10.0;
  float num2 = 10.0f;
  double num3 = 1.1f;
  double num4 = .123; // 将num4赋值为0.123
```

5. 科学计数法

e2 = 10^2    E-2 = 10^-2

```java
System.out.println(5.12e2);
System.out.println(512e-2);
// 输出
512.0
5.12
```

对于科学计数法这一直接使用e的方式我今天才知道原来在go语言中也适用...

6. **浮点数陷阱**

对于两个浮点数不要进行**比较运算**，而应该是通过两个数的差值的绝对值是否在某个精度范围内来作为判断依据

原因是对于**被除数**，计算机**无法判断**其在我们所看到的**最后一位小数之后是否还有数字**

同样，这一点我也是今天才发现在go语言中这个问题也存在...

Go:

![](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231101193445753.png)

Java:

![image-20231101193514510](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231101193514510.png)

正确的判断两个浮点数是否相等的写法:

```java
if (Math.abs(num5 - num6) < 0.0001) {
    System.out.println("两者相等");
}
```

### 字符型

- char(两个字节) 存放单个字符，可以是汉字

  ```java
    char c1 = 'a';
    char c2 = '\t';
    char c3 = '嗨';
    char c4 = 97;
  ```

  输出:

  ![image-20231101194648036](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231101194648036.png)

char不能严格类比go语言中的类型，但在字符集这一方面，可以认为:

​    byte&rune -> char

因为char既可以处理utf-8(**unicode的最多编码数量是65535个字符**)也可以处理ascii字符集(一共有128个值，**是8位二进制数到英语字符的映射集合**，最前面1位统一规定为0)

不过**go中的rune占四个字节长度，byte占一个字节长度**，这么一想，类比的话还是byte更合适些，但是在输出是，**char会直接输出整数对应的unicode字符**，这是一个特殊的地方

#### 使用规则

1. java中赋予了char使用转义字符'\\'的能力，来将其后的字符转为特殊字符型常量
2. java中char**本质是一个整数**，在输出时输出其所对应的unicode字符

### 布尔值类型

布尔值类型**均是1个字节**

- true
- false

用法和go中几乎没有区别

只不过声明的时候go中是`bool`，而java中是`boolean`

还有在内存上的区别: go中的布尔占4个字节，而java中占1个字节

### 数据类型自动转换

核心原则: `精度小的类型可以自动转换为精度大的类型`

![image-20231101201344298](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231101201344298.png)

如:

```java
int a = 'a';
double d = 80;
```

#### 使用规则

1. 注意点: 当有多种数据类型的数据进行混合运算时，jvm会将所有非容量最大的数据的类型转化为其中容量最大的数据类型后，再进行运算

   ```java
    float f = 'a' + 1.1; // 会报错，因为最大的容量的数据类型是double，float装不下
   ```

2. 如果给1中运算的结果赋予了比起精度小的数据类型，那么会报错的，否则会自动进行类型转换

3. (byte、short)和char之间不能进行自动转换(其实这感觉是java设计中的比较有意思的地方)，但是你可以通过强转来实现char和byte的相互转换

   ```java
   char b = 'b';
   System.out.println((int)b);
   byte c = 99;
   System.out.println((char)c);
   // 输出
   98
   c
   ```

### 数据类型强制转换

强转是自动转换的逆过程，即将容量大的数据类型转换为容量小的数据类型，但有可能会造成精度丢失或数据溢出

```java
int i = (int)1.9;
System.out.println(i); // 1 精度损失
int n = 2000;
byte b = (byte)n;
System.out.println(b); // -49 数据溢出
```

和go中的用法也是很类似的，只不过就是语法上有有区别，java中的强转符需要写到值的前面

##### 使用规则

1. 需要注意的一个例子，是否需要强装需要考虑数据是否会溢出，比如int -> char这种情况就会溢出

   ```java
   char d;
   int f = 100;
   d = f; // 会报错，溢出了
   d = (char)f; // 需要通过强转
   System.out.println(d);
   // 输出
   d
   ```

### 基本数据类型与String之间的转换

对于基本数据类型的变量转String，**只需+ ""即可**

对于String转基本数据类型，需要通过基本数据类型的包装类调用parsexx方法

且对于parsexx方法传入的参数，我们需要先人为判断是否能转为对应的xx类型，否则jvm会抛出异常，且在未被捕捉的情况下会终止程序，即这是一个**运行时错误**

```java
int num1 = Integer.parseInt(s2);
double num2 = Double.parseDouble(s2);
float num3 = Float.parseFloat(s2);
long num4 = Long.parseLong(s2);
byte num5 = Byte.parseByte(s2);
boolean b = Boolean.parseBoolean("true");
short num6 = Short.parseShort(s2);
```

对于char类型，则取String中的第一个字符

## 引用数据类型

Java中的引用数据类型有诸如类、接口、数组，会再后续的篇幅中进一步学习

# 运算符

## 算数运算符

和go中基本一致，除了前++与后++，因为go中是没有前++的，因此这里的重点是温习前++与后++

1. ++作为独立语句使用

   ```java
   int i = 1;
   i++; // i = i + 1
   ++i; // i = i + 1
   ```

   那么前++与后++的含义是等价的

2. ++作为表达式使用

   ```java
   int j = 2;
   int k = ++j; // j = j + 1; -> k = j
   int m = j++; // m = j; -> j = j + 1 
   ```

   区别在于：

   前++: 原变量先加1，再将增加后的原变量的值赋给新变量，**先自增，再赋值**

   后++: 先将原变量的值赋给新变量，再将原变量的值加1，**先赋值，再自增**

这里有个有很意思的问题，就是**赋值的对象是自己**：

```java
int i = 1;
i = i++;
```

上述代码执行完后，i的值是多少？

在这里会有一个较为特殊的规则:

会产生一个隐式的新变量temp，有:

```java
temp = i;
i = i + 1
i = temp
```

因此，**i的值是1**

那对于前++呢？

```java
int i = 1;
i = ++i;
```

相应的，会有:

```java
i = i + 1
temp = i
i = temp
```

因此，对于前++，**i的值是2**

## 关系运算符&逻辑运算符&赋值运算符

和go中完全一致

![image-20231103144225146](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231103144225146.png)

1) a&b : & 叫逻辑与：规则：当 a 和 b 同时为 true ,则结果为 true, 否则为 false 
2) a&&b : && 叫短路与：规则：当 a 和 b 同时为 true ,则结果为 true,否则为 false 
3) a|b : | 叫逻辑或，规则：当 a 和 b ，有一个为 true ,则结果为 true,否则为 false 
4) a||b : || 叫短路或，规则：当 a 和 b ，有一个为 true ,则结果为 true,否则为 false 
5) !a : 叫取反，或者非运算。当 a 为 true, 则结果为 false, 当 a 为 false 是，结果为 true 
6) a^b: 叫逻辑异或，当 a 和 b 不同时，则结果为 true, 否则为 false

&& 和 & 使用区别 

- &&短路与：如果第一个条件为 false，则第二个条件不会判断，最终结果为 false，效率高 

- & 逻辑与：不管第一个条件是否为 false，第二个条件都要判断，效率低

||和|的区别也是同理

有一点是需要注意的，**在复合赋值是，底层会自动进行类型转换**

```java
byte b = 3; // b的数据类型是byte
b += 2; // b的数据类型会因为有int类型的2参与运算，而转换为int
// 即 b = (byte)(b + 2)
```

## 三元运算符

这是go中所不支持的，因此也需要再温习一下

### 基本语法

`条件表达式?表达式1:表达式2;`

- 如果条件表达式为true，则运算后的结果是表达式1
- 如果条件表达式为false，则运算后的结果是表达式2

```java
int a = 1;
int b = 2;
int result = a > b? a++:b--;
System.out.println(result); // 2
```

需要注意的是，表达式1和2的数据类型都需要是可以匹配新变量的数据类型(包括自动转换)

比如，你不能:

```java
int a = 1;
int b = 2;
int c = a > b ? 1.1:2.2;
```

需要注意的是，**三元运算符是一个整体，因此其中的表达式可能会提高被赋值变量的精度**，比如若表达式1是一个int，而表达式2是一个double，那么即使条件选中的是表达式1，被赋值的精度会是和double相同

## 比较运算符

### ==和equals方法

对于==: 

既能比较两个基本类型也能比较引用类型

- 如果比较的两者是基本类型，则判断的是两者的**值**是否相等

  ```java
  int a = 10;
  int b = 10;
  System.out.println(a == b) // true
  ```

- 如果比较的两种是引用类型，则判断是地址是否相等，即指向的是否是同一个对象

  ```java
  A a = new A();
  A b = a;
  A c = a;
  System.out.println(c == b) // true
  ```

对于equals方法: 

equals()是Object类中的方法，**只能判断引用类型**，其源码是:

```java
public boolean equals(Object anObject) {
    if (this == anObject) { // 如果传入的参数是当前对象，则返回true
        return true;
    }
    return false;
}
```

但**equals方法往往会被重写**，用于赋予更多的能力或改变逻辑以满足在不同类中判断逻辑，这其中往往会需要**向下转型**(会在后面的篇幅中详细讨论)后去判断两者值是否相同，以JDK 11中String类的equals()的源码为例，其重写的目的是在判断两者是否是同一对象的基础上加了一层判断两者值是否相等的逻辑:

```java
public boolean equals(Object anObject) {
    if (this == anObject) { // 如果传入的参数是当前对象，则返回true
        return true;
    }
    if (anObject instanceof String) { // 判断是否是String类型或其子类
        String aString = (String)anObject; // 向下转型
      	// 判断字符串中的值是否相等
        if (coder() == aString.coder()) {
            return isLatin1() ? StringLatin1.equals(value, aString.value)
                              : StringUTF16.equals(value, aString.value);
        }
    }
    return false;
}
```

# 标识符命名规范

- 包名: 多单词组成时，所有字母都**小写**: aaa.bbb.ccc，如 `com.ryan.crm`
- 类名、接口名: 多单词组成时，所有单词的首字母大写，即**大驼峰**: XxxYyyZzz，如`StudentManagerSystem`
- 变量名、方法名: **驼峰式写法**，如`isSuccess`
- 常量名: 所有**字母都大写+蛇形写法**，如TAX_RATE

## 关键字

![image-20231103151017340](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231103151017340.png)

![image-20231103151042454](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231103151042454.png)

## 保留字

Java 保留字：现有 Java 版本**尚未使用**，但**以后版本可能会作为关键字使用**。自己命名标识符时要避免使用这些保留 

字 byValue、cast、future、 generic、 inner、 operator、 outer、 rest、 goto 、const

# 键盘输入

即接收控制台输入的变量，也是必须要掌握的，毕竟笔试是避免不了ACM模式的

java中需要借助JDK中的Scanner类，其在java.util.Scanner包下

```java
import java.util.Scanner;
```

这里的scanner的设计和用法和go语言标准库中的bufio.Scanner是很像的，下面的是一个在java中接收字符串、整型、薪水的例子(一行一个参数)

```java
Scanner myScanner = new Scanner(System.in);

String name = myScanner.next();
int age = myScanner.nextInt();
double salary = myScanner.nextDouble();
```

# 进制

二进制：0,1 ，满 2 进 1.以 0b 或 0B 开头。 

十进制：0-9 ，满 10 进 1。 

八进制：0-7 ，满 8 进 1. 以数字 0 开头表示。 

十六进制：0-9 及 A(10)-F(15)，满 16 进 1. 以 **0x** **或** **0X** 开头表示。此处的 A-F 不区分大小写。

## 原码、反码、补码复习

![image-20231103154415022](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231103154415022.png)

## 位运算

和go中的基本一致，这里的篇幅也主要是复习的目的

需要注意的两点是: 

- 计算时以**补码**计算
- 运算结果是补码计算后的**原码**

**&运算例子**

```java
// 2 & 3的运算过程
// 2的原码: 00000000 00000000 00000000 00000010 因为2是正数，2的补码也是如此
// 3的原码: 00000000 00000000 00000000 00000011
// 因此运算后的补码是: 00000000 00000000 00000000 00000010 因为其是一个正数，故其的原码也是如此，也就是最后的结果
System.out.println(2&3); // 2
```

**~运算例子**

```java
// ~-2的运算过程
// -2的原码: 10000000 00000000 00000000 00000010
// -2的反码: 11111111 11111111 11111111 11111101 (符号位不变，其他位取反)
// -2的补码: 11111111 11111111 11111111 11111110 (反码+1)
// -2取反:  00000000 00000000 00000000 00000001 因为其是一个正数，故其的原码也是如此，也就是最后的结果(符号位为0)
System.out.println(~-2); // 1
```

**算数右移&左移**

- 算数右移>>: 低位溢出，符号位不变，并用符号位补溢出的高位(除2)
- 算数左移<<: 符号位不变，低位补0 (乘2)
- 无符号右移>>>: 低位溢出，高位补0 (go中没有)
- java中没有<<<

```java
System.out.println(1>>2); // 0
System.out.println(1<<2); // 4
```

# 流程控制

## 分支控制

### if语句

```java
if (条件表达式) {
		执行代码块;
} else if (条件表达式2) {
  	执行代码块;
} else {
  	执行代码块;
}
```

和go唯一的区别是条件表达式部分需要用小括号括起来

```java
Scanner myScanner = new Scanner(System.in);
int num = myScanner.nextInt();
if (num == 100) {
    System.out.println("信用极好");
} else if (num < 100 && num > 80) {
    System.out.println("信用优秀");
} else if (num <= 80 && num >= 60) {
    System.out.println("信用一般");
} else {
    System.out.println("信用不及格");
}
```

### switch语句

还有`switch`，和go中的语法基本是相似的这里不做介绍

其实我自己写go从没写过switch，不过go中的`select`的语法和switch很相像

需要注意的是

- switch中的表达式的返回值必须是byte、short、int、char、enum、String，**不能是double**
- case字句中的值必须是常量，而不能是变量
- 记得选择是否要写break

还有一个**switch穿透的例子**

```java
int month = myScanner.nextInt();
switch (month) {
    case 3:
    case 4:
    case 5 :
        System.out.println("春季");
        break;
    case 6:
    case 7:
    case 8 :
        System.out.println("夏季");
        break;
}
```

即一个case中如果没有break语句，则会继续执行接下来的case

### 规范性

考虑到可读性以及可维护性，**建议嵌套不要超过3层**

## 循环控制

### for循环

go中的for循环是比较独特的，有三种用法，而java中的for循环的用法也可分为大致三类

1. 写明for循环中的所有元素

```java
for (循环变量初始化; 循环条件; 循环变量迭代) {
		循环操作;
}
```

2. java中也可以像go一样，在for循环中仅写明部分元素(将变量初始化和变量迭代的部分写到其他地方)，但不能省略分号:

```java
// 没有变量初始化
int i = 0;
for (; i < 10;) {
    System.out.println(123);
    i++;
}
```

但此时，ide会提升建议直接使用`while`，可能这是更适合while语义的场景

![image-20231106163704998](/Users/steaksunflower/Library/Application Support/typora-user-images/image-20231106163704998.png)

3. java中有增强的for循环`for each`，有些类型于go中的`for range`

   ```java
   int[] array = {2, 3, 1, 4, 5, 5, 6};
   for (int element : array) {
       System.out.println(element);
   }
   ```

   其应用场景是**依次处理数组(集合)中的每个元素**，而不必考虑指定的下标值

   和go中如下列中这样应用`for range`是一样的效果:

   ```go
   array := []int{2, 3, 1, 4, 5, 5, 6}
   for _, element := range array {
     	fmt.Println(element)
   }
   ```

不过java中**不能像go中的for一样用作死循环**

```java
// 死循环
for {

}
```

java中for循环也有go中不能做的，**java中的for循环初始语句可以有多条，中间用逗号隔开，但要求类型一样，变量迭代部分也可以用多条语句，中间也用逗号隔开**

```java
int count = 5;
for (int i = 0, j = 0; i < count; i++, j+=2) {
    System.out.println("i=" + i + "j=" + j);
}
// 输出:  
// i=0j=0
// i=1j=2
// i=2j=4
// i=3j=6
// i=4j=8
```

### while循环

和go中的for死循环用法基本一样

```java
循环变量初始化;
while(循环条件) {
		循环体;
		循环变量迭代;
}
```

需要注意的是，要在while循环外部初始化变量

```java
int i = 0;
while (i != 3) {
    i++;
}
```

### do-while循环

这是go中所没有的，适用于一些特殊场景

```java
循环变量初始化;
do {
		循环体;
		循环变量迭代;
} while(循环条件);
```

**和while区别是:**

while是**先判断再执行**，do-while是**先执行再判断**，也就是说**一定至少会执行一次**

```java
int i = 1;
do {
    System.out.println(123);
} while (i == 0);
// 控制台输出
// 123
```

break和continue和go中完全一致，就不占用篇幅了

不过关于break、continue，java中还有关于**标签(label)**的用法，可以指定退出到代码中的那一部分，不过不常使用，感兴趣的读者可以自行了解

### 规范性

建议一般使用两层，**不要超过3层**，否则O(n3)的时间复杂度，代码执行效率低，易读性也会较差
