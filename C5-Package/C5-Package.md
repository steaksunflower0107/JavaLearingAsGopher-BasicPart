# 包

包是模块化编程的基础，也是任何大型项目最基本的架构单位，java中的包关键字和go中一样都是`package`，通过`import`关键字引入包

在go中，不同的包下，我们可以命名相同名称的结构体或函数

对应的在java中，不同的包下，我们可以命名相同名称的类

## 命名规则

- 在java的src目录下，创建包时`.`代表目录分隔符，如创建包名为com.google表示创建了com、google两个目录，其中com是google的父目录

- 命名规范: com.公司名.项目名.模块名，如

  ```java
  com.meituan.crm.user //用户模块
  com.meituan.crm.order // 订单模块
  ```

  java作为在业务开发中最主流的语言，包的命名规范是参考了URL的设计，故源码中的根目录往往是com，如果你所开发的项目的顶级域名是net，那可能根目录的名称会是net

- java在编码过程中会隐式自动引入`java.lang.*`包，其中包含了很多常用的api，而在go中，任何包都需要显式引入

- java中相对于go在引入过程中可以精确到某个类，如`java.util.Scanner`，而在go中引入的最小单位只能是包，以在做k8s开发时常常会引入的sigs包为例`sigs.k8s.io/controller-runtime/pkg/client`

## 