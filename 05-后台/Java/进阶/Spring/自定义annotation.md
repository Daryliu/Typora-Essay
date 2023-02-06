[TOC]

# 自定义annotation

自定义注解的时候使用四个注解：*@Target*，*@Retention*，*@Documented* 和*@Inherited*。

## *@Target*

用于描述注解的使用范围（即：被描述的注解可以用在什么地方）

可见*@Target* 注解只有唯一成员value，类型为ElementType数组。查看ElementType的源码可以发现，ElementType可取的值有：

1. **CONSTRUCTOR**：用于描述构造器；
2. **FIELD**：用于描述成员变量；
3. **LOCAL_VARIABLE**：用于描述局部变量；
4. **METHOD**：用于描述方法；
5. **PACKAGE**：用于描述包；
6. **PARAMETER**：用于描述参数；
7. **TYPE**：用于描述类、接口(包括注解类型) 或enum声明。

## *@Retention*

指定被描述的注解在什么范围内有效。

其中RetentionPolicy可取的值有：

1. **SOURCE**：在源文件中有效（即源文件保留）；
2. **CLASS**：在class文件中有效（即class保留）；
3. **RUNTIME**：在运行时有效（即运行时保留）。

## *@Documented*

是一个标记注解，木有成员，用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化。

## *@Inherited*

元注解是一个标记注解，*@Inherited*阐述了某个被标注的类型是被继承的。如果一个使用了*@Inherited*修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。