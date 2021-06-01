## 狂神Java

#### 数据类型扩展

- 少用浮点数进行比较；若真的比较最好完全使用浮点数进行比较（float是有限的，存在误差，会舍入误差，大约接近但不等于）

- 所有的字符本质还是数字，可以（int）强制转换为数字

- 布尔值扩展

  ```java
  boolean flag = true;
  if (flag ==true) {}			//等价于
  if (flag) {}
  ```

#### 类型转换

- byte->short,char->int->long->float->double（从低到高转换，**高到低**需要强制类型转换）
- 强制类型转换时注意“内存溢出”（即需要转换的的数据超过要转换类型的大小）和“精度问题”
- 不能对布尔值类型进行转换；不能把对象类型转换为不相干的类型
- 操作比较大的数时注意溢出问题，计算时需要提前将数据进行转换（JDK7新特性，数字之间可以用下划线来分割1000000000等价于10_0000_0000）

#### 变量

- 每个变量都有类型，类型可以是基本数据类型，也可以是引用类型（string，类、数组）
- 变量必须是合法的标识符；且以分号结尾
- 类变量（`static int a = 0;`）、实例变量String str = "hhh";（从属于对象，用的话需要先new对象用；**有默认值**）、局部变量（必须声明和初始化）
- **常量**：初始化之后不能改变，使用`final`来定义

#### 方法

> 注：println输出会换行、print输出不会
>
> **return除了返回结果，也可以“终止方法”**
>
> Java是**值传递**

##### 方法重载

- 方法名称相同
- 参数列表必须不同（个数、类型、排列顺序）
- 方法返回类型可以相同也可以不同
- **仅返回值不同不足以构成重载**



###### （了解）命令行传参：

```java
public static voiid main(String args[]) {
    for(int i = 0;i < args.length; i ++) {
        System.out.println(i + args[i]);
    }
}

然后在命令行中或者找到文件所在位置的src，进行javac xxx.java编译---->java xxx运行
```

###### 可变参数

**因为如果只是变量数量不同（不确定会传多少参数）而进行多次方法重载显得代码冗余。所以使用可变参数。**

- 在方法声明中，在指定参数类型后加一个省略号

- 一个方法中只能指定一个可变参数，它必须是方法的最后一个参数，任何普通参数必须在他之前声明

- ```java
  public static void printMax(double... numbers) {
      if (numbers.length == 0) {
          System.out.println("No argument passed");
          return;
      }
      
      double result = numbers[0];
      
      for(int i =0; i< numbers.length; i++) {
          if(number[i] >result) {
              result = numbers[i];
          }
      }
      System.out.println("最大值为" + result);
  }
  
  
  调用时可以写成：printMax(new double[]{1,2,3})
  ```

##### 递归

1. 递归头：什么时候不调用自身方法。如果没有头，将陷入死循环
2. 递归体：什么时候需要调用自身方法

