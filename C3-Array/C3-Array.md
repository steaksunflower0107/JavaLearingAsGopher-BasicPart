java中的数组和go中的数组是类似的，**数组是存放多个同一类型的数据的容器**，由此便于用户在内存中存储数据与集成数据方便后续操作，是**引用类型**(go中的数组是值类型)，关于什么是java中的引用类型会在之后的篇幅中详细学习

```java
double[] stocks = {1.1, 2.2, 3.5, 7.2};
for (int i = 0; i < stocks.length; i++) {
    System.out.println(stocks[i]);
}
```

# 动态初始化

1. 声明时直接new出来

   ```java
   int[] array = new int[5];
   ```

2. 先声明再创建

   ```java
   // 声明
   int array[]; // c语言样式，在java中不推荐
   int[] array; // java样式
   // 创建
   array = new int[5];
   ```

# 静态初始化

适用于已经知道数组中的所有元素的场景

```java
int[] array = {2, 3, 1, 4, 5, 5, 6};
```

## 规范与细节

- 数组中的**元素可以是任何数据类型**，包括基本类型和引用类型，但要保证一个数组中的元素类型唯一

- 不同于变量的初始化，如果你已经创建了一个数组，且没有进行赋值操作，那么数组中的所有元素的值是对应数据类型的零值

  ```java
  int -> 0
  short -> 0
  byte -> 0
  long -> 0
  float -> 0.0
  double -> 0.0
  char -> \u0000
  boolean -> false
  String -> null
  ```

- 数组在java中时引用类型，虽然还没有深究理解java中的引用类型的含义，但在**数组赋值**时可以理解为被赋值的数组是一个指针，**指向了原数组**，所以在改变被赋值数组中的元素时，**原数组中的元素也会发生改变**

  ```java
  int[] arr1 = {1,2,3};
  int[] arr2 = arr1;
  arr2[0] = 10;
  // arr1: 10,2,3
  // arr2: 10,2,3
  ```

  在ide中也有相应的提示，表示这是一个冗余的操作:

<img width="748" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/15e204ce-199f-405f-9ef1-b9742f41f3e1">


**应用: 冒泡排序示例**

```java
class BubbleSort {
    public static void main(String[] args) {
        int[] nums = {24, 67, 81, 57, 11};
        int temp = 0;
        for (int i = 0; i < nums.length-1; i++) {
            for (int j = 0; j < nums.length - i - 1; j++) {
                if (nums[j] > nums[j+1]) {
                    temp = nums[j];
                    nums[j] = nums[j+1];
                    nums[j+1] = temp;
                }
            }
        }

        System.out.println(Arrays.toString(nums));
    }
}
```

# 二维数组

关于二维数组，就以一个例子来有一个印象，对于应用其实参照go中或者在写其他语言时的思想就好了

```java
int[][] arr = new int[3][]; // 此时尚未初始化二维数组中的行元素
for (int i = 0; i < arr.length; i++) {
  	// 初始化二维数组中的行元素
    arr[i] = new int[i+1];
		// 给列赋值
    for (int j = 0; j < arr[i].length; j++) {
        arr[i][j] = i + 1;
    }
}
```

且在java中有一种相对于go比较独特的声明方式

```java
int[] x[] // x是一个int类型的二维数组变量
```

## 二维数组的静态初始化

还有值得一提的是，关于二维数组的静态初始化，和go中是很像的

```java
int[][] arr = {{1,2,3}, {2,3,1}, {3,5,1}};
```

在ide中刷题时，可能会常常使用到这种初始化的方法
