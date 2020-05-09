<center>Java</center>

### 方法

<!--一段用于完成特定功能的代码片段-->；方法的返回值可直接用于计算或使用。

```java
[修饰符]  方法返回值类型  方法名(形参列表) {
    // n条语句
}
```

###### 可变参数的调用

```java
public static void main(String[] args)//二者性质上一样，但是其初始化是要定义为空或者null
public static void main(String... args)//可变类型参数的传递 ...表示参数为0至多个，没有参数可不填（若有多个参数，则可变参数要放在最后）
```

###### 方法的参数传递

附注：方法只有被调用才有意义；

<1>基本参数传递方式：<u>值(变量在栈内存中的值)传递</u>，将实参的副本传入方法，而实参本身不受影响

<2>引用对象的参数传递：实参（实际的对象）向形参传参时，也是把值给形参，这个值是实参在栈内存中值（也就是引用对象在堆内存中的地址）

<img src="C:\Users\daryl\AppData\Roaming\Typora\typora-user-images\image-20200430192050144.png" alt="image-20200430192050144" style="zoom: 50%;" />

#### 1、方法重载（overload）  

方法名字相同，形参类型、个数、顺序不同，只有返回值不同不构成重载

###### 递归

需要确认什么时候开始和结束<!--减少使用，速度慢耗内存-->

###### Static

定义方法时加了static定义后，其他方法可直接调用，不用创建对象



---

### 面向对象和面向过程

<1>宏观上使用面向对象把握，微观上处理任然是面向过程(例如“如何造车”和“如何开车”)

面向过程：怎么按步骤实现，并将步骤对应成方法，最终完成。<!--适合简单，不需要协作的事务-->

面向对象：

#### 对象object

定义：将相关“数据和相关方法”封装到一个独立的实体。内存上对象就是一个内存块

#### 类class

定义：对象的对象，即是对对象的抽象；对象是具体的事物。

内容：属性/成员变量；方法；构造方法<!--(用于创建这个类的对象，无参的由系统创建)-->

<!--在其中一个类中一定要有main方法，程序执行的入口-->

<!--一个.Java文件只能有一个public修饰的类，但是可有N个类。-->

***使用一个类的时候需要创建这个类的对象，然后使用这个类的成员变量和方法***

##### 构造方法constructor

定义：用于创建和对象和初始化

附注：（修饰符、方法名）与类名完全一致；<u>构造方法内return不能有返回值；</u>new对象就是调用类的构造方法

构造方法的重载与方法重载一致（为了方便调用方灵活的创建出不同需要的对象）

当存在显示的构造方法时，类就没有无参构造了，只使用显示构造。

#### JavaBean  <!--实体类-->

类是共有的、有一个无参的共有的构造函数、<u>属性</u>一般是<u>私有</u>的且<u>有对应的set<设置属性>和get方法<取属性></u>。

```java
public class Person {    //一个JavaBean
    private String name;  //私有的属性
    //对应的set、get方法
    public void setName(String name){		
        this.name = name;
    }
    
    public String getName(){
        return this.name;
    }
}
```

#### 对象的实例化过程

```java
Person p = new Person();//实例化过程如下
```

<img src="C:\Users\daryl\AppData\Roaming\Typora\typora-user-images\image-20200501214028806.png" alt="image-20200501214028806" style="zoom:50%;" />



##### 面向对象的内存分析

内容：栈、堆、方法区(属于堆)

栈stack：是方法执行的内存模型，方法调用时会创建一个栈帧(局部变量、操作数、<u>方法</u>出口)；每个线程私有(实际存的是地址，它即引用对象，真正的对象值在堆中)

堆heap：<u>存储创建好的对象</u>；所有线程共享；不连续，分配灵活速度慢

方法区：存放永远不变或唯一的内容(class类信息、static静态变量、字符串常量)

![image-20200430174930541](C:\Users\daryl\AppData\Roaming\Typora\typora-user-images\image-20200430174930541.png)



##### 垃圾回收机制garbage collection

<u>程序员无权调用垃圾回收器</u>

对象空间的分配：使用new关键字创建对象即可

对象空间的释放：将对象赋值null即可。<u>垃圾回收器</u>将负责回收所有”不可达”对象的内存空间

###### 分代的垃圾回收

年轻代(Eden区)：新生成的对象，收集声明周期短的对象    Minor GC

年老代：经历了N次垃圾回收的对象     Major GC和Full GC(全量回收)

持久代：存放静态文件，如java类、方法

```git
	1、新创建的对象，绝大多数都会存储在Eden中，

    2、当Eden满了（达到一定比例）不能创建新对象，则触发垃圾回收（GC），将无用对象清理掉，

           然后剩余对象复制到某个Survivor中，如S1，同时清空Eden区

    3、当Eden区再次满了，会将S1中的不能清空的对象存到另外一个Survivor中，如S2，

          同时将Eden区中的不能清空的对象，也复制到S1中，保证Eden和S1，均被清空。

    4、重复多次(默认15次)Survivor中没有被清理的对象，则会复制到老年代Old(Tenured)区中，

    5、当Old区满了，则会触发一个一次完整地垃圾回收（FullGC），之前新生代的垃圾回收称为（minorGC）
```



### 关键字

##### this    <!--不可用于static方法中-->

this的本质就是“创建好的对象的地址”，在<u>方法内部</u>使用，即这个方法所属对象的引用；

<u>构造方法中</u>的this可以代表“当前对象”；

附注：当形参和成员变量重名时，需要添加this来表明该变量时类成员。

<u>this调用重载的构造方法时用于构造方法第一句</u>。

##### static

1、声明静态成员变量，随着类加载而加载，<u>优于对象存在</u>，生命周期与类相同，是整个程序执行期间，也称为<font color="yellow">“类变量/静态变量”。直接<u>类名.属性(或者方法)</u>就可以使用，如static String country;</font><font color="#00ff85"><u>被这个类的所有实例化对象所共享</u></font>

2、需要实例化的称为“实例变量”，如String name;需要实例化才能使用，不能共用

3、<u>由其定义的方法不能使用非静态的方法</u>

类属性：不因对象的不同而改变；<u>类方法<font color="yellow">（做工具类）</font></u>：方法与调用者无关，方法不因对象的不同而频繁new对象来调用，就写成static

##### super

1、访问父类中定义的属性、方法、构造器

2、子父类出现同名时用super区分

3、this代表对象本身的引用，super代表父类的内存控件的标识

4、使用<u>super子类可以调用子类之上的所有父类层级</u>

附注：<1>子类中所有的构造器默认访问父类中无参的构造器

​			<2>当父类没有空参数的构造器时，子类的构造器必须通过this(参数列表)或super(参数列表)语句指定本类或父类中的构造器，且必须放在构造器的第一行

```java
父类：   //显示有参构造
    public class ManKind{
        public ManKind(int sex, int salery){
            this.sex = sex;
            this.salery = salery;
        }
    }
//父类只有有参构造时，子类必须显示构建一个有参的构造方法来调用父类的构造。且要在第一行
子类：
    public class Kids extends ManKind{
    public Kids(int sex, int salery){
        super(sex,salery);
    }
}
```

​			<3>若子类构造器中既未显示调用父类或本类中的构造器，且父类中又没有无参的构造器则编译出错

| NO.  | 区别       | this                                                         | super                    |
| ---- | ---------- | ------------------------------------------------------------ | ------------------------ |
| 1    | 访问属性   | 访问本类中的属性，<u>如果本类没有此属性则从父类中继续查找</u> | 访问父类中的属性         |
| 2    | 调用方法   | 访问本类中的方法                                             | 直接访问父类中的方法     |
| 3    | 调用构造器 | 调用本类构造器，必须放在构造器首行（构造器中二者只可一个存在，因为都要在首行） | 调用父类构造器，位于首行 |
| 4    | 特殊       | 表示当前对象                                                 | 无此概念                 |

##### final

final标记的类为“最终”，不可被继承；final标记的方法不能被子类重写；final标记的变量成为常量，名称大写且要显示赋值（直接赋值），只能被赋值一次；（final static一起修饰的变量是全局常量）



***

### 包package和引用import

定义：包与文件夹功能类似，亦可有多级；

```java
demo包下定义一个包test，则demo.test创建   //多级的包使用.分隔
//同一个包下默认引用可以省略
import demo.test.Person  //导入demo包下的test包下的Person.java文件
import demo.test.*  //引入其包下的所有文件    
```

***

#### 封装和隐藏private

对不能让调用者随意使用的属性做封装和隐藏

<u>封装和隐藏后不可调用则需要提供public的方法getXXX()和setXXX()实现对该属性的操作</u>（set和get方法可自己手动生成）

```java
例如：private int age;
public void setAge(int a){
    if(a<=100 &&a>=0)
        System.outprintln("");
}
```

#### 权限修饰符

|    修饰符     | 类内部 | 同一个包 | 子类 | 任何地方 |
| :-----------: | ------ | -------- | ---- | -------- |
|    private    | √      |          |      |          |
| default(缺省) | √      | √        |      |          |
|   protected   | √      | √        | √    |          |
|    public     | √      | √        | √    | √        |

<font color="green">对于class类的权限修饰只可是public和default(缺省)。</font>

<!--一个.Java文件只能有一个public修饰的类，但是可有N个类。-->

##### 父子类与访问修饰符的关系

1、如果子类和父类在同一个包下，那么只有父类的成员修饰符不是private，子类都可以使用

2、如果子类和父类不在同一个包下，那么子类只能使用父类中protected和public修饰的成员变量和方法

---

#### 继承extends

共性代码继承于父类/基类/超类，子类中写特有的代码；子类不能直接访问父类中私有的成员变量

<u>继承了父类的方法和属性，子类是父类的扩展，而不是其子集</u>

作用：<1>提高了代码的复用性

​			<2>让类与类之间产生关系，提供了多态的前提

​			<3><u>不要仅为了获取其他类中的某个功能去继承</u>

##### 单层继承和多层继承

Java只支持单继承，不支持多继承，即<u>一个子类只能有一个父类</u>，可以多层继承

#### 重写overwrite       ALT+/

定义：子类根据需要对从父类继承的方法进行改造，执行时，子类的方法会覆盖父类的方法

附注：1、重写方法必须和被重写方法有相同的方法名称、参数列表、返回值类型（只是重写方法体）

​			2、重写方法不能使用比被重写方法更严格的访问权限（若父为public，则子不可使用public以下的）

​			3、重写和被重写方法必须同时为static或同时非static

​			4、子类抛出的异常不能大于父类被重写方法的异常

#### 多态

定义：多态是指同一个方法的调用，由于对象不同可能会有不同的行为。（属性无多态。方法有）

<u>前提：要有继承、重写、父类类型指向子类对象</u>

Java引用变量会出现<u>编译时类型和运行时类型</u>，编译时类型由声明该变量时使用的类型决定，运行时类型由实际赋给该变量的对象决定；若编译时类型和运行时类型不一致就出现多态。【对象的多态】

成员变量（属性）不具备多态性；

成员方法的多态性也就是动态绑定，必须存于方法的重写之上<u>(也就是若Person p= new Student();父类想用子类的方法，子类方法必须是重写父类的方法)</u>（既然已经重写父类方法，那也就覆盖了，也只能用子类的方法）

```java
//子类可以看作特殊的父类，所以父类类型的引用可以指向子类的对象，即向上转型。
Person p= new Person();		1、继承  2、重写  3、父类类型指向子类对象
Person p= new Student();//自动向上转型   	父类类型p指向子类对象Student
Student s = (Student)p;//强制向下转型   同级之间不可转型
//但若声明的是父类类型，实际引用的却是子类对象，则该变量就不能访问子类中的属性和方法  即p不可访问Student中的属性和方法？？？？
//***属性是在编译时确定的，编译时p为Person类型；方法是在运行时确定的，运行时为Student类型***
```



---

##### Instanceof操作符

```java
Student a = new Student();
Person p = new Person();
a instanceof Person;  //检测x是否为A的对象，返回值为Boolean型    左边是对象，右边是类
//***x对象所属的类与A类必须是子类和父类的关系；***
//若x属于A的子类B，则为true
```

##### Object类

定义：是所有Java类的根父类。没有指明继承的父类时，默认继承object类

```java
private void method(obejct obj);	//在不知道传什么类类型的参数时，可以确定传递实参一定会是一个类，那么形参使用object来定义
```

| NO   | 方法名称                          | 类型 | 描述                                       |
| ---- | --------------------------------- | ---- | ------------------------------------------ |
| 1    | public Object()                   | 构造 | 构造方法                                   |
| 2    | public boolean equals(Object obj) | 普通 | 引用对象比较                               |
| 3    | public int hashCode()             | 普通 | 取得Hash码                                 |
| 4    | public String toString()          | 普通 | 对象打印时调用，返回当前引用对象的内存地址 |

##### 对象类型转换

1、自动类型转换：小的数据类型自动转换为大的数据类型；

2、强制类型转换：大的数据类型强制转化为小的数据类型；(casting)a

3、对Java对象的强制类型转换(造型)

```java
//<1>从子类到父类自动转换；
//<2>从父类到子类的转换需要造型；
Person p = new Person();
Student s= (Student)p;
//<3>无继承关系的引用类型之间转换时非法的；
```

##### 操作符==与equals方法

1、==

基本类型比较：只要两值相等，则为true；

引用类型比较（是否指向同一个对象）：<u>只有指向同一对象才为true；</u>在内存中，==的new的String生成不同的对象

2、equals()

只能比较引用类型，比较（是否指向同一个对象）：<u>只有指向同一对象才为true；</u>

特殊：对于对象而言，特殊的类如String、File、Date使用==比较的是对象的地址，equals比较的是内容；除此外对象二者比较的对象的地址；若想改变每一类的equals需要重写。

![image-20200502110236956](C:\Users\daryl\AppData\Roaming\Typora\typora-user-images\image-20200502110236956.png)

##### 包装类Wrapper

| 基本数据类型 | 包装类（基本数据类型的引用类型（封装成类）） |
| ------------ | -------------------------------------------- |
| boolean      | Boolean                                      |
| byte         | Byte                                         |
| short        | Short                                        |
| int          | Integer                                      |
| long         | Long                                         |
| char         | Character                                    |
| float        | Float                                        |
| double       | Double                                       |

```java
基本数据类型包装成保“包装类”---装箱    （装箱拆箱在jdk1.5后自动实现）
int i = 500; Integer t = new Integer(i);		//***Integer i = 11;自动装箱
	可以通过字符串参数构造包装类对象
	Float f = new Float("4.56");//4.56已变成Float类
获得包装类对象中包装的基本类型变量---拆箱
boolean b = new Boolean("false").booleanValue();//调用各自的xxxValue  int i1 = i;自动拆箱
		//包装类的重要点：
***字符串转化为基本数据类型   Xxx.parseXxx***
    int i = Integer.parseInt("123");或者float f = Float.parseFloat("0.40");
***基本数据类型转化为字符串   valueOf***
    String str = String.valueOf(i);
```

##### toString

没有重写toString时输出当前对象的内存地址；直接输出对象时默认输出对象.toString；

若要输出l类的其他信息需要重写toString的方法；

***

#### 单例设计模式

定义：采取一定的方法保证在整个软件系统中，<u>对某个类只能存在一个对象实例</u> ，并且该类只提供一个取得其对象实例的方法；（因此要将类的构造方法的访问权限设置为private，这样就不能用new操作符在类的外部产生类的对象。）

解决的问题：1、new对象太费劲 2、频繁的new新对象没有必要

```java
public class Single{
    //饿汉式单例模式   一开始就创建一个对象
    private Single(){		//构造方法私有化，调用这个类的人就不能直接new来创建对象
    }
    private static Single single = new Single();//私有的single类型的“类变量”
    public static Single getInstance(){
        return single;   //由于构造函数和类变量都是私有的，只能通过getInstance函数来使用，所有都指向new Single()
    }
    -----------------------------------------------------------------------------------------
    //懒汉式单例   最开始对象是null直到有第一个人调用才new一个对象，以后所有调用的都用这个对象
    private Single1(){//构造方法私有化，调用这个类的人就不能直接new来创建对象
    }
    private static Single1 single1 = null;
    public static Single1 getInstance(){
        if(single1 == null){
            single1 = new Single1();//第一个进方法的为空，则new一个；后面的都共享这个类变量，后面的都调用第一个的对象和方法
        }
        return single1;
    }
}
```



#### main()方法

```java
public static void main(String[] args){
    //因为java虚拟机在执行main时不需要创建对象，所以static表明通过类名.方法名即可调用
}
```

#### 抽象类

定义：将一个父类设计的十分抽象，没有具体的实例；

用abstract来修饰；<u>抽象方法只有方法声明，没有方法实现；还有抽象方法的类必须为抽象类</u>

<u><font color="green">抽象类不可实例化，是用来作为父类被继承的，其子类必须重写抽象类的抽象方法并提供方法体</font></u>（或者还写抽象方法）；

不能用abstract修饰属性、私有方法、构造器、静态方法、final的方法    抽象类可继承抽象类

```java
//例如  Animal void move()动物类移动方式没法直接写，所有动物类和其方法都是抽象化处理，就不具体实现，到具体的动物后再具体实现********************重点************************
//子类Dog void move()  Fish void move()  Bird void move()
```

```java
public abstract class Animal{//抽象类案例
    public abstract void test();//无方法体
    
    public abstract void move();
}
class Dog extends Animal{
    @Override
    public abstract void test(){//子类继承抽象类之后必须重写抽象方法
        
    }
    @Override
    public abstract void move(){
        System.out.println("狗跑");
    }
}
class Fish extends Animal{
    @Override
    public abstract void test(){//子类继承抽象类之后必须重写抽象方法
        
    }
    @Override
    public abstract void move(){
        System.out.println("鱼游");
    }
}

```

#### 模板方法设计模式

抽象类体现的就是一种模板模式，抽象类作为多个子类的通用模板，子类在抽象类的基础桑扩展，但子类总体上保留了抽象类的行为方式。（抽象类就是大纲，子类将大纲细化）

解决的问题：1、当功能一部分实现确定，一部分实现不确定，可以把不确定的部分暴露出去，让子类实现2、抽象父类提供多个通用方法，并把一个或多个暴漏给子类实现

```java
abstract class Template{   //模板设计模式案例
    public final void getTime(){
        long start = System.currentTimeMills();
        code();
        long end = System.currentTimeMills();
        System.out.println("执行时间是"+(end-start));
        public abstract void code();//父类不实现
    }
    class SubTemplate extends Template{
        public void code(){//子类来实现
            for(int i = 0;i < 10000; i++){
                System.out.println(i);
            }
        }
    }
}
```

---

#### 接口		（解决单继承的局限）

定义：是特殊的抽象类，<font color="green"><u>只包含常量和方法的定义，没有变量和方法的实现</u></font>；

特点：1、用interface定义 2、成员变量默认public static final修饰 3、方法默认public abstract修饰 4、没有构造器（<u>但是可以对象多态，new对象实现调用方法</u>） 5、采用多层继承机制

<u>子类继承父类，只能继承一个父类；类可以实现多个接口，多个接口用，分割</u>（接口可以继承接口）

```java
public interface Runner{   
    int ID = 1;			 ------>默认为public static final int ID = 1;
    void start();		 ------>public abstract void start();//没有方法体
    public void run();	 ------>public abstract void run();
}

/*------------------------------在另一个类中实现接口---------------------------------*/
public class TestIn implements Runner{//public class TestIn implements Runner,Test继承Runner,Test两个接口，要将接口中所有的方法体实现（如果没实现所有方法，那么就要将没实现的方法定义为抽象方法*******）(如果一个类既继承又实现接口，则先继承后实现)
    @Override
    public void run(){  
        //实现所有方法的方法体
    }
    @Override
    void start(){
        //方法体
    }
}
```

<1>接口的主要用途就是被实现类实现（<u>父类需要的是稳定的抽象</u>，但有时需要在父类上添加一些方法，那么就不能直接在父类上下手，只能新建一个接口在接口上扩展方法，其他需要的子类自行实现接口方法体）

<2>接口与实现类之间存在多态性（继承、重写、父类调用子类）

<font color="yellow">抽象类是对一类事物的高度抽象，其中既有属性也有方法；接口时对方法的抽象，也就是对一系列方法的抽象。        当需要对一类事物抽象时使用抽象类形参父类；当需要对一系列的动作抽象，需要使用接口，并使用这些动作的类实现接口</font>

***

#### 工厂方法模式

？？？？？？？？

***

#### 内部类

1、允许一个类的定义位于另一个类的内部，前者成为内部类，后者成为外部类（<u>内部类可以调用外部类所有变量</u>）；

2、内部类可声明为final、private、protected、static(此时不可使用外层类的非static的成员变量)、可声明为abstract，因此可被其他内部类继承

3、<font color="yellow">内部类的作用</font>:解决Java不能<u>多重继承</u>的问题（<u>当类A想继承类B和类C并重写二者的方法，但是不能多重继承，就在类A中写内部类B1继承类B，类A中写内部类C1继承类C，重写其方法</u>）

```java
public class Test{//外部类
    int i;
    public int z;
    private int k;
    class A{//内部类
        public void setTest(){
            Test.this.i = 1;//内部调用外部属性时用  外部类名.this.属性
            Test.this.z = 1;
            Test.this.k = 1;
        }
    }
    public void setInner(){
        new A().setTest();//外部调用内部类A的方法，要先new内部类的对象
    }
    public void showInfo(){
        System.out.println(this.i);//打印i的值
    }
}
```

---

### 异常

1、Error：JVM系统内部错误、资源耗尽等；

2、Exception：其他因编程错误或偶然的外在因素导致的一般性问题，如空指针访问、试图读取不存在的文件、网络连接中断（程序员只能处理Exception）

##### 捕获异常try  catch

```java
try{		//用try来编写有可能出现异常的代码段   
}catch(Exception e){//catch捕获异常，多个catch只捕获第一个异常   所有的异常有一个父类Exception（当不知道捕获的是什么类型的异常时，就直接使用异常的父类Exception）  
}finally{//可写可不写   写了之后就是异常中一定会执行的一部分
}
```

##### 抛出异常

父类在抛出异常时，子类也必须抛出异常，且子类重写方法不能比父类抛出异常范围大

```java
public class Test{
    public static void main(String[] args){//最好不在main时抛出异常，直接抛给虚拟机了，不能再程序中处理了
        B b = new B();
        try{ //throws在代码中的抛出异常，在调用时捕获处理异常
            b.test();
        }catch(Exception e) {
        }
    }
}
class B {
    int i;
    public void test() throws Exception{//可以使用throws在代码中抛出异常，在调用方捕获处理异常
        B b = null;
        System.out.println(b.i);
    }
}
```



***

###### 使用scanner获得键盘输入

```java
Scanner scanner = new Scanner(System.in);
System.outprintIn("输入名字:");
String name = scanner.nextLine();
```

最初执行Java文件的过程javac xxx.java编译,  java xxx运行

```java
//判断一个字符串是否为空
  if(s != null &&s.equals(""))  
```

