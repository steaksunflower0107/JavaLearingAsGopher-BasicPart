# 反射

反射是在基础研发中的非常重要的机制，也是实现动态语言的重要部分，反射机制提供了**我们可以在不修改源码的情况下，使程序可以动态地运行不同逻辑的能力**

下面通过一个例子来初步了解反射机制

譬如，现有一配置文件，指定了`Person`的全路径，以及其方法`selfIntroduce`的路径

```properties
classFullPath = javaReview.Reflection.ReflectorPractice.Person
method = selfIntroduce
```

我们可以根据文件I/O读取该配置文件到内存中(注意抛出相关异常):

```java
// 读取配置
Properties properties = new Properties();
properties.load(new FileInputStream("classPath"));
String classFullPath = properties.get("classFullPath").toString();
String method = properties.get("method").toString();
System.out.println(classFullPath); // javaReview.Reflection.ReflectorPractice.Person
System.out.println(method); // selfIntroduce
```

此时`classFullPath`与`method`，都是String类型，仅能作为路径的标识使用，但java中提供的反射机制**可以使得仅有类、方法的路径我们即可加载它并使用**

```java
// 加载类，forName方法返回一个Class类型的对象
Class classA = Class.forName(classFullPath);
// 通过Class类型的对象创建加载的类对象
Object object = classA.newInstance();
// 确定对象的运行类型
System.out.println("object's running type is " + object.getClass()); // object's running type is class javaReview.Reflection.ReflectorPractice.Person
// 通过Class类型的对象创建方法对象
Method methodA = classA.getMethod(method);
// 通过方法对象去调用方法
methodA.invoke(object);
```

JDK提供`Class`类和`Method`类以及其相关方法实现了反射，在用户的视角上，更是践行了java的设计哲学即`万物皆对象`的设计理念，通过反射机制，一个类也可以成为一个对象`classA`，一个方法也可以是一个对象`methodA`，在调用方法时，相比于传统的`对象名.方法名`去调用，通过方法对象可以通过`方法名.invoke(对象名)`去调用

诸如`classA`和`methodA`这样的类对象，**分别可以成为任何类、方法的代理**

至此，在上述的例子中，如果希望更改被调用的方法，就**只需改变配置文件中的信息并重启即可**，命令行参数为程序注入了在不修改源码的情况下改变参数值的能力，而反射注入了不修改源码即在**运行时的情况下改变类、方法的能力，**

实现反射的原理可以简述为: java文件需要编译成.class文件才能被jvm加载使用,**对象的.class数据在jvm里就是`Class<T>`**，可以被认为是**类对象**，如果能获取这个`Class<T>`对象，**就能通过反射获取该`Class<T>`对应的对象类型**，及在该类型声明的方法和属性值；还可以根据`Class<T>`创建相应的类型对象，通过Field,Method反过来操作对象

对象与对象类型是反射机制中的两个主体，就如同我们与镜子里的我们一样，我们可以看到镜子里的我们的所有部分，反过来，镜子中的我们也可以看到我们所有的部分，**你可以看到镜子，镜子也可以看到你**

但反射提供这种便捷操作也是有相应的代价的，由此反射实现的是一种**解释执行**，**使用反射程序的效率会有所下降**

## 反射相关类

能被反射的不止是类和方法，主要有以下几种:

- `java.lang.Class`: 即类对象，表示某个类在**类加载阶段**在堆中生成的对象

- `java.lang.reflect.Method`: 即方法对象

- `java.lang.reflect.Field`: 即类成员对象(字段)，值得注意的是，**通过反射不能得到私有的字段**

  ```java
  Field fieldAge = classA.getField("age");
  System.out.println(fieldAge.get(object)); // 获取某个对象的字段值
  fieldAge.set(object, 18); // 设置字段值
  ```

- `java.lang.reflect.Constructor`: 即构造器对象

  比如，对于`Person`类的两个构造器，分别是无参和有参:

  ```java
  public Person() {}
  
  public Person(String name, int age) {
      this.age = age;
      this.name = name;
  }
  ```

  通过反射机制获取:

  ```java
  Constructor constructorWithNoPara = classA.getConstructor(); // 返回无参构造器对象
  System.out.println(constructorWithNoPara); // public javaReview.Reflection.ReflectorPractice.Person()
  
  Constructor constructorWithPara = classA.getConstructor(String.class, int.class); // 返回有参构造器对象
  System.out.println(constructorWithPara); // public javaReview.Reflection.ReflectorPractice.Person(java.lang.String,int)
  ```

## 反射优化-关闭访问检查

上述有提到过使用反射的负面效应会降低程序的执行效率，下述代码我们大致量化测试一下反射的负面效应:

```java
// 测试类
class ReflectionTest {
		// 不使用反射
    public void noReflection() {
        Person ryan = new Person();
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000000000; i++) {
            ryan.selfIntroduce();
        }
        long endTime = System.currentTimeMillis();
        System.out.println("invoke method with no reflection takes " + (endTime - startTime));
    }
		// 使用反射
    public void useReflection() throws ClassNotFoundException, InstantiationException, IllegalAccessException, InvocationTargetException, NoSuchMethodException, IOException {
        Properties properties = new Properties();
        properties.load(new FileInputStream("/Users/steaksunflower/Java/javaStudy/src/javaReview/Reflection/reflector.properties"));
        String classFullPath = properties.get("classFullPath").toString();

        Class personClass = Class.forName(classFullPath);
        Object ryan = personClass.newInstance();
        Method selfIntroduce = personClass.getMethod("selfIntroduce");


        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000000000; i++) {
            selfIntroduce.invoke(ryan);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("invoke method with reflection takes " + (endTime - startTime));
    }
}
```

结果如下:

```java
ReflectionTest reflectionTest = new ReflectionTest();
reflectionTest.noReflection(); // invoke method with no reflection takes 6
reflectionTest.useReflection(); // invoke method with reflection takes 1784
```

可见效率显著降低

为了弥补反射降低的效率，通过JDK原生提供的方法我们可以关闭访问检查来实现

JDK为`Method`、`Field`、`Constructor`对象都提供了`setAccessible()`方法，作为**启动和关闭访问检查的开关**，为true时表示关闭访问检查，以此可以提高使用反射情况下的执行效率

如在上述例子中加入:

```java
selfIntroduce.setAccessible(true); // 取消访问检查
```

结果如下:

```java
ReflectionTest reflectionTest = new ReflectionTest();
reflectionTest.noReflection(); // invoke method with no reflection takes 5
reflectionTest.useReflection(); // invoke method with reflection takes 863
```

### 暴破

`setAccessible()`方法传入`true`时还可以做到**访问private修饰的构造器、方法、字段**，暴力破解

比如`Person`类的一个私有构造器:

```java
private Person(String name) {
    this.name = name;
}
```

通过暴破访问:

```java
Constructor<?> constructor = classA.getDeclaredConstructor(String.class);
constructor.setAccessible(true);
System.out.println(constructor); // private javaReview.Reflection.ReflectorPractice.Person(java.lang.String)
```

暴破私有方法、字段的方式也是同理，获取对应类型的对象调用`setAccessible()`传入`true`即可

## Class类

Class类是java程序被JVM执行过程中十分重要的原生类，在类加载阶段，.class文件中的类会被**类加载器(ClassLoader)**通过`loadClass()`方法在堆中加载出**类对象**，即Class类的对象，该对象包含了类中的信息，供**运行时**的对象使用

值得注意的是**每个类的类对象只会被加载一次**，无论这一次使用传统调用还是反射调用

通过Class类的对象，我们可以换取完整的类信息，以下是Class类中常见的方法，供我们在使用反射时选择:

| 方法名                                                       | 描述                                         |
| :----------------------------------------------------------- | :------------------------------------------- |
| `getName()`                                                  | 返回类的完全限定名                           |
| `getSimpleName()`                                            | 返回类的简单名称（不包含包名）               |
| `getPackage()`                                               | 返回类所在的包                               |
| `getSuperclass()`                                            | 返回类的父类                                 |
| `getInterfaces()`                                            | 返回类实现的接口                             |
| `getConstructors()`                                          | 返回类的公共构造方法                         |
| `getDeclaredConstructors()`                                  | 返回类声明的所有构造方法（包括私有构造方法） |
| `getMethods()`                                               | 返回类的公共方法                             |
| `getDeclaredMethods()`                                       | 返回类声明的所有方法（包括私有方法）         |
| `getFields()`                                                | 返回类的公共字段                             |
| `getDeclaredFields()`                                        | 返回类声明的所有字段（包括私有字段）         |
| `newInstance()`                                              | 创建类的新实例                               |
| `isInterface()`                                              | 判断是否为接口类型                           |
| `isArray()`                                                  | 判断是否为数组类型                           |
| `isPrimitive()`                                              | 判断是否为基本数据类型                       |
| `isAnnotation()`                                             | 判断是否为注解类型                           |
| `isEnum()`                                                   | 判断是否为枚举类型                           |
| `isAssignableFrom(Class<?> cls)`                             | 判断当前类是否可以分配给指定类或接口         |
| `getAnnotation(Class<A> annotationClass)`                    | 返回指定类型的注解                           |
| `getAnnotations()`                                           | 返回类声明的所有注解                         |
| `isAnnotationPresent(Class<? extends Annotation> annotationClass)` | 判断是否存在指定类型的注解                   |

### 获取Class类对象

通过反射可以丰富我们获取类对象的方式以及提供了在java进程的运行过程中不同阶段获取类对象的能力，本小节用于做一次汇总:

1. 已知某个类的路径，通过Class类的静态方法`forName()`获取，需抛出`ClassNotFoundException`异常，常应用于文件配置，在编译阶段即可进行:

   ```java
   Class personClass = Class.forName(classFullPath);
   ```

2. 通过`类名.class`获取，常应用于参数传递，如通过构造器对象创建对象时，该信息是在类加载阶段进行的:

   ```java
   Constructor constructorWithPara = classA.getConstructor(String.class, int.class); // 返回有参构造器对象
   ```

3. 内存中已有某个类的实例，通过`实例名.getClass()`方法获取，这也是在多态章节中获取对象的运行类型的方法:

   ```java
   Person person =  new Person();
   Class personClass = person.getClass();
   ```

4. 对于基本数据类型，可以类似2一样通过`基本数据类型.Class`获取Class类对象

   ```java
   Class<Integer> integerClass = int.class;
   Class<Character> characterClass = char.class;
   Class<Boolean> booleanClass = boolean.class;
   ```

5. 基本数据类型的包装类，除了通过2中的方式，还可以通过`类名.Class`获取类对象

   ```java
   Class<Integer> integerClass = Integer.TYPE;
   Class<Character> characterClass = Character.TYPE;
   ```

类对象还可以通过**类加载器**获取，这也是JVM在加载类时所默认使用的方式，关于类加载器的内容后续会在学习JVM的部分

除了自定义类、基本数据类型、包装类，对于**接口、注解、enum、内部类、数组，甚至void**，都可以通过`.Class`去获取它们的类对象，再次印证了java的设计理念，**万物皆对象**

## 类加载

类加载分为**静态加载**与**动态加载**，前者在编译期就会完成检查，而后者通过反射机制实现，在运行时才会被检查，这里的特性是可以被利用的，比如如果有一段逻辑不确定有用户会访问到，我们可以采用动态加载的方式以在不会有用户访问的情景下减少堆内存的消耗

以下是类加载的过程:

- **加载阶段（Loading）**

  1. 查找并加载类的字节码文件: java虚拟机根据类的全限定名在class文件、jar包、网络或其他来源中**查找并读取类的字节码文件到内存中**。字节码文件通常以.class文件形式存在
  2. 创建对应的Class对象: 一旦字节码文件被获取，**java虚拟机会将其转化为一个Class对象**，这个Class对象包含了类的结构信息，并用于在运行时动态创建对象

- **验证阶段（Verification）**

  1. 文件格式验证: java虚拟机对字节码文件的格式进行验证，确保它符合java虚拟机规范定义的文件格式要求
  2. 字节码验证: 验证字节码的逻辑合法性，避免潜在的类型安全问题
  3. 符号引用验证: 对类中的符号引用进行验证，确保引用的目标是有效的
  4. **内部一致性验证**: 验证类的内部结构是否一致，比如父类与子类之间的继承关系是否正确

- **准备阶段（Preparation）**

  1. 为静态变量分配内存空间: **java虚拟机为类的静态变量在内存中分配空间**，这些变量被存储在方法区中
  2. 设置默认初始值: **静态变量被初始化为默认值**，比如整数类型变量初始化为0，引用类型变量初始化为null

- **解析阶段（Resolution）**

  1. 符号引用转换为直接引用: java虚拟机**将常量池中的符号引用转换为直接引用**，以便后续使用
  2. 类、接口、字段和方法解析: 将符号引用解析为对应的类、接口、字段和方法的直接引用，以便进一步操作

- **初始化阶段（Initialization）**

  **从该阶段开始是用户可以指定的，也就是用户编码开始被执行**

  **执行静态变量赋值和静态代码块**：java虚拟机执行类的初始化代码，给静态变量赋予初始值，并执行静态代码块中的代码。这些代码通常用于**完成静态变量的初始化以及其他一次性的初始化工作**

- **使用阶段（Usage）**

  1. 创建实例对象: 在类加载完成后，可以通过构造函数创建类的实例对象
  2. 调用方法: 通过对象调用类的方法
  3. 访问字段: 通过对象访问类的字段（成员变量）







