# Java的跨平台性

java的跨平台性体现于：.java文件在编译后会成为.class(**字节码文件**)文件(类似go build后的.exe文件)，.class文件可以在不同平台上运行(如linux、windows)，这是**基于Java虚拟机实现**的

**JVM**

是一个虚拟的计算机，有它自己的指令集并使用不同的存储区域，负责执行代码中指令、管理数据、内存、寄存器，其包含在JDK中
对于不同的平台(os)，需要安装不同的JVM，其屏蔽的了底层运行平台的差异，实现了跨平台性

**常用命令**

javac - 编译(go build)

java - 运行(go run)

javap - 反编译(go不支持)

**JDK&JRE**

JDK = JRE + javaSE 开发工具(java、javac、javadoc、javap等)

​	类似于go的SDK

JRE = JVM + JavaSE的核心类库，只要一个os中具备JRE，就可以运行编译好的.class文件

​	类似于go的runtime

# Java基本规则

1.一个.java文件中只能有**一个public类**，其他类的个数不限

2.如果.java文件中有一个public类，则**文件名与类名**必须一致

3.java中的入口也是main方法，但不一定需要放在public类中，放到哪个类里哪里就是入口，且可以有多个main方法，用户可以自行选择jvm的执行入口

4.java源文件使用utf-8编码

5.特殊的加号

java作为一门强类型的语言，"+"具有它的特殊之处

- 当相加的变量中有一方为字符串时，则做拼接运算
- 当相加的变量都是数值类型时，则作加法运算

注意，以上法则以前后顺序在相邻两个变量中生效

```java
System.out.println(100 + 3 + "hello");
System.out.println("hello" + 100 + 3);
```

输出:

```shell
103hello
hello1003
```

而这种用法，在go中是会报错的，go中严格要求+衔接的两个变量的类型一致，比如int和float用加号衔接是不支持的

对于第三点，和go的差别还是蛮大的，因为go中一个包只允许有一个main方法