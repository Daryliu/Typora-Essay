[TOC]



##### <font color = "greenbule">toString</font>

​	默认返回   类名+@+hashcode

​	`return getClass().getName() + "@" + Integer.toHexString(hashCode());`

​	例如：类名为Test  则返回`Test@1b6d3586`

​	

##### <font color = "greenbule">"=="与"equals"</font>

​	"=="代表比较双方是否相同。如果是基本类型则表示<u>值</u>相等，如果是引用类型则表示<u>地址</u>相等即是同一个对象。

​	 equals 方法默认就是比较两个对象的hashcode，是同一个对象的引用时返回 true 否则返回 false

```java
public class TestEquals { 
    public static void main(String[] args) {
        Person p1 = new Person(123,"高淇");
        Person p2 = new Person(123,"高小七");     
        System.out.println(p1==p2);     //false，不是同一个对象
        System.out.println(p1.equals(p2));  //true，id相同则认为两个对象内容相同
        
        //JDK提供的如String、Date、包装类等重写了equals方法，调用这些时若双方是同一对象且属性内容相等时返回true
        String s1 = new String("尚学堂");
        String s2 = new String("尚学堂");
        System.out.println(s1==s2);         //false, 两个字符串不是同一个对象
        System.out.println(s1.equals(s2));  //true,  两个字符串内容相同
    }
}
class Person {
    int id;
    String name;
    public Person(int id,String name) {
        this.id=id;
        this.name=name;
    }
    public boolean equals(Object obj) {
        if(obj == null){
            return false;
        }else {
            if(obj instanceof Person) {
                Person c = (Person)obj;
                if(c.id==this.id) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

​	JDK提供的如String、Date、包装类等重写了equals方法，调用这些时若双方是<u>同一对象且属性内容相等</u>时返回true



##### <font color = "greenbule">静态导入</font>

​	`import static Java.lang.Math.*;`寻找Math类中的所有静态属性



##### <font color = "greenbule">继承树追溯</font>

​	**1、构造方法调用顺序**[从父到子]

​		先向上追溯到Object，然后再依次向下执行类的初始化块和构造方法，直到当前子类为止。

​		注：静态初始化块调用顺序，与构造方法调用顺序一样，不再重复。

​	**2、属性/方法查找顺序（如：查找变量a）**[从子到父]

- 查找父类当中有无属性a
- 依次上溯每个父类，查看每个父类中是否有h，直到Object
- 若未找到则出现编译错误（找到则终止）



##### <font color = "greenbule">多态</font>

​	继承，方法重写，父类引用指向子类对象



##### <font color = "greenbule">对象的转型</font>

​	父类引用指向子类对象，我们称这个过程为<u>向上转型</u>，属于自动类型转换。

```java
Object obj = new String("aaa"); // 向上可以自动转型
```

   向上转型后的父类引用变量只能调用它编译类型的方法，不能调用它运行时类型的方法。这时，我们就需要进行类型的强制转换，我们称之为<u>向下转型</u>!

```java
 String str = (String) obj; // 向下转型；必须将引用变量转成真实的子类类型(例如int string，而不是stringbuffer)；为避免这种异常需要使用instanceof
```



##### <font color = "greenbule">final</font>

1. 修饰变量: 被他修饰的变量不可改变。一旦赋了初值，就不能被重新赋值。
2. 修饰方法：该方法不可被子类重写。但是可以被重载!
3. 修饰类: 修饰的类不能被继承。比如：Math、String等。



##### <font color = "greenbule">抽象方法和抽象类</font>

​	使用abstract修饰的方法，<u>没有方法体，只有声明</u>。定义的是一种“规范”，就是告诉子类必须重写抽象类的抽象方法并提供方法体。



##### <font color = "greenbule">接口（不全）</font>

​	接口中所有方法都是抽象方法，且接口不提供任何实现



##### <font color = "greenbule">Scanner</font>

​	获取用户的输入

​	next() 与 nextLine() 方法<u>获取输入的字符串</u>，在读取前我们一般需要 使用 hasNext 与 hasNextLine 判断是否还有输入的数据[next()不能读取空格及后面的字符串]

```java
//创建对象
Scanner sc = new Scanner(System.in);
//接收数据
int x = sc.nextInt();
```



##### <font color = "greenbule">StringBuffer和StringBuilder</font>

​	可变的字符序列

​	String 类不同的是，StringBuffer 和 StringBuilder（线程不安全，但是速度快） 类的对象<u>能够被多次的修改，并且不产生新的未使用对象。</u>

​	`append()`添加字符串序列

​	`delete(int start,int end)`删除从start开始到end-1的字符序列

​	`deleteCharAt(int index)`移除此序列指定位置的char

​	`insert(…)`在指定位置插入字符序列

​	`reverse()`用于将字符序列逆序

##### <font color = "greenbule">String</font>

​	String 类对象代表不可变的Unicode字符序列，因此我们可以将String对象称为“不可变对象”。**指的是对象内部的成员变量的值无法再改变。**<u>String一经初始化后，就不会再改变其内容了。对String字符串的操作实际上是对其副本(原始拷贝)的操作，原来的字符串一点都没有改变</u>；<u>其他方法例如substring()</u>是对字符串的截取操作，但<u>本质是读取原字符串内容生成了新的字符串</u>。

​	字符串池：new的不同对象会存在不同的地址中

```java
String s9 = "hello";	//字符串常量
String s7 = new String("hello");	//使用new关键字创建
```

**字符串拼接**：使用+运算符或String的concat(String str)方法。 +运算符优势是可以连接任何类型数据拼接成为字符串， 而concat方法只能拼接String类型字符串。  



##### <font color = "greenbule">模板方法模式和回调函数</font>

​	**回调**：A类中调用B类中的C方法，然后B类中的C方法中反过来调用A类中的D方法，那么D这个方法就叫回调方法。

1. Class A实现接口CallBack —— 背景1

2. class A中包含class B的引用 ——背景2

3. class B有一个参数为CallBack的方法C ——背景3

   4. 前三条是我们的准备条件，接下来A的对象调用B的方法C

5. 然后class B就可以在C方法中调用A的方法D

```java
/** 
 * 回调接口  
 */
interface CallBack {  
    /** 
     * 小高知道答案后告诉小刘时需要调用的方法，即回调方法
     * @param result 是问题的答案 
     */  
    public void answer(String result);  
}
/** 
 * 小刘类：实现了回调接口CallBack（背景一） 
 */  
class Liu implements CallBack {  
    /** 
     * 包含小高对象的引用 （背景二） 
     */  
    private Gao gao;   
  	//构造函数
    public Liu(Gao gao){  
        this.gao = gao;  
    }  
       
    /** 
     * 小刘通过这个方法去问小高 
     * @param question  小刘问的问题“学习Java选哪家机构呢？” 
     */  
    public void askQuestion(String question){  
        //小刘问小高问题
        gao.execute(Liu.this, question);          
    }  
    /** 
     * 小高知道答案后调用此方法告诉小刘
     */  
    @Override
    public void answer(String result) {//实现接口的函数
        System.out.println("小高告诉小刘的答案是：" + result);        
    }  
} 
/** 
 * 小高类 
 */  
class Gao {
     /** 
     * 相当于class B有一个参数为CallBack的方法C（背景三） 
     */  
    public void execute(CallBack callBack, String question){  
        System.out.println("小刘问的问题是：" + question);  
        //模拟小高挂点后先办自己的事情花了很长时间  
        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        //小高办完自己的事情后想到了答案 
        String result = "学Java当然去北京尚学堂";  
        //小高打电话把答案告诉小刘，相当于class B 反过来调用class A 的D方法 
        callBack.answer(result);         
    }  
}
 
public class Test {
    public static void main(String[] args) {
        Gao  gao= new Gao();  
        Liu liu = new Liu(gao);  
        //小刘问问题
        liu.askQuestion("学习Java选哪家机构呢？"); 
    } 
}
```



#### <font color = "greenbule">异常</font>

​	Java是采用<u>面向对象的方式来处理异常</u>的。

​	1. **抛出异常：**在执行一个方法时，如果发生异常，则这个方法生成代表该异常的一个对象，停止当前执行路径，并把异常对象提交给JRE。

2. **捕获异常：**JRE得到该异常后，寻找相应的代码来处理该异常。JRE在方法的调用栈中查找，从生成异常的方法开始回溯，直到找到相应的异常处理代码为止。

   **RuntimeException**（运行时异常）：通常是由编程错误导致的，

   - ArithmeticException异常：试图除以0
   - NullPointerException异常------------>增加非空判断
   - ClassCastException异常
   - ArrayIndexOutOfBoundsException异常------------>增加关于边界的判断，与xxx.length判断
   - NumberFormatException异常

   **CheckedException**已检查异常：必须处理的异常，否则无法编译

异常处理方式：

​	1、捕获异常

​	2、声明异常（throws子句）：向上传递给调用他的方法处理

```java
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
 
public class Test9 {
    public static void main(String[] args) {
        try {
            readFile("joke.txt");
        } catch (FileNotFoundException e) {
            System.out.println("所需文件不存在！");
        } catch (IOException e) {
            System.out.println("文件读写错误！");
        }
    }  
    //如果一个方法中可能产生某种异常，但是并不能确定如何处理这种异常，则应根据异常规范在“方法的首部声明该方法可能抛出的所有异常，用逗号隔开。”
    public static void readFile(String fileName) throws FileNotFoundException, IOException {
        FileReader in = new FileReader(fileName);
        int tem = 0;
        try {
            tem = in.read();
            while (tem != -1) {
                System.out.print((char) tem);
                tem = in.read();
            }
        } finally {
            in.close();
        }
    }
}
```

​	附注：方法重写中声明异常原则：子类重写父类方法时，如果父类方法有声明异常，那么子类声明的异常范围不能超过父类声明的范围。



##### <font color = "greenbule">自定义异常</font>

​	1.在程序中，可能会遇到JDK提供的任何标准异常类都无法充分描述清楚我们想要表达的问题，这种情况下可以创建自己的异常类，即自定义异常类。

   2.自定义异常类只需从Exception类或者它的子类派生一个子类即可。

   3.自定义异常类如果继承Exception类，则为受检查异常，必须对其进行处理;如果不想处理，可以让自定义异常类继承运行时异常RuntimeException类。

   4.习惯上，自定义异常类应该包含2个构造器：一个是默认的构造器，另一个是带有详细信息的构造器。

```java
/**IllegalAgeException：非法年龄异常，继承Exception类*/
class IllegalAgeException extends Exception {
    //默认构造器
    public IllegalAgeException() {
     
    }
    //带有详细信息的构造器，信息存储在message中
    public IllegalAgeException(String message) {
        super(message);
    }
}
```



#### <font color = "greenbule">数组</font>

​	合法区间：**[0, length-1]**

​	声明数组的时候没有任何实例化对象，只有在实例化数组对象时，JVM才分配空间。

```java
int[] s = null; // 声明数组；===》在栈内存
s = new int[10]; // 给数组分配空间；====》在堆内存创建
```

引用类型的一维数组

```java
class Man{
    private int age;
    private int id;
    public Man(int id,int age) {
        super();
        this.age = age;
        this.id = id;
    }
}
public class AppMain {
    public static void main(String[] args) {
        Man[] mans;  //声明引用类型数组； 
        mans = new Man[10];  //给引用类型数组分配空间；
         
        Man m1 = new Man(1,11);
        Man m2 = new Man(2,22);  
         
        mans[0]=m1;//给引用类型数组元素赋值；
        mans[1]=m2;//给引用类型数组元素赋值；
    }
}
```

​	

**for-each**

​	1. for-each增强for循环在遍历数组过程中不能修改数组中某元素的值。

2. for-each仅适用于遍历，不涉及有关索引(下标)的操作。

- **数组拷贝**

  System类里也包含了一个static void arraycopy(object src，int srcpos，object dest， int destpos，int length)方法。

  Object src : 原数组
  int srcPos : 从元数据的起始位置开始
  Object dest : 目标数组
  int destPos : 目标数组的开始起始位置
  int length  : 要copy的数组的长度

- **Java.util.Arrays类**

  1. 打印数组

     `System.out.println(Arrays.toString(a));` // 打印数组元素的值；=====》打印为[1，2]

  2. 数组元素排序

     `Arrays.sort(a);`

  3. 数组元素是引用类型的排序*************************

     ```java
     import java.util.Arrays;
     public class Test {
         public static void main(String[] args) {
             Man[] msMans = { new Man(3, "a"), new Man(60, "b"), new Man(2, "c") };
             Arrays.sort(msMans);
             System.out.println(Arrays.toString(msMans));
         }
     }
      //--------------------Comparable接口的应用-----------------------
     class Man implements Comparable {
         int age;
         int id;
         String name;
      
         public Man(int age, String name) {
             super();
             this.age = age;
             this.name = name;
         }
      
         public String toString() {
             return this.name;
         }
      	//通过compareTo比较一个属性来排序
         /*定义一个比较器
         *0：相等
         *1：前者大于后者
         *-1：前者小于后者
         */
         public int compareTo(Object o) {
             Man man = (Man) o;
             if (this.age < man.age) {
                 return -1;
             }
             if (this.age > man.age) {
                 return 1;
             }
             return 0;
         }
     }
     ```

  4. 二分查找法-----------`Arrays.binarySearch(a, 12));` //查找数组a中值为12的索引

     ```java
     import java.util.Arrays;
     public class Test {
         public static void main(String[] args) {
             int[] a = {1,2,323,23,543,12,59};
             System.out.println(Arrays.toString(a));
             Arrays.sort(a);   //使用二分法查找，必须先对数组进行排序;
             System.out.println(Arrays.toString(a));
             //返回排序后新的索引位置,若未找到返回负数。
             System.out.println("该元素的索引："+Arrays.binarySearch(a, 12)); 
         }
     }
     ```

  5. 数组填充

     `Arrays.fill(a, 2, 4, 100);` //将数组a中2到4索引的元素替换为100;



##### <font color = "greenbule">二维数组</font>

```java
// Java中多维数组的声明和初始化应按从低维到高维的顺序进行
int[][] a = new int[3][];
a[0] = new int[2];
a[1] = new int[4];
a[2] = new int[3];
```

​	**获取数组长度**：

```java
//获取的二维数组第一维数组的长度。
System.out.println(a.length);
//获取第二维第一个数组长度。
System.out.println(a[0].length);
```



#### <font color = "greenbule">包装类</font>

​	Java是面向对象的语言，但并不是“纯面向对象”的，因为我们经常用到的基本数据类型就不是对象。但是我们在实际应用中经常需要将基本数据转化成对象，以便于操作。

![表8-1基本数据类型对应的包装类.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495593568889579.png)

​	**用途**：

			1. 作为和基本数据类型对应的类型存在，<u>方便涉及到对象的操作</u>，如Object[]、集合等的操作。
   			2. 包含每种基本数据类型的相关属性如最大值、最小值等，以及相关的操作方法(这些操作方法的作用是在基本数据类型、包装类对象、字符串之间提供相互之间的转化!)。



​	`Integer int3 = Integer.parseInt("334");`===>字符串转化成Integer对象，   `Integer.parseInt(String s ,int radix)`，radix用来表示传进来的值是什么进制的，并返回10进制的 **int** 类型的结果**。** 

​	`String str1 = int3.toString();`===>Integer对象转化成字符串

​	`xx.intValue`===>将另一种对象类型转换为int



​	**自动装箱**：通过调用包装类的valueOf()方法实现的

```Java
Integer i = 100;//自动装箱
//相当于编译器自动为您作以下的语法编译：
Integer i = Integer.valueOf(100);//调用的是valueOf(100)，而不是new Integer(100)
```

​	**自动拆箱**：通过调用包装类的 xxxValue()方法实现的(xxx代表对应的基本数据类型，如intValue()、doubleValue()等)

```Java
Integer i = 100;
int j = i;//自动拆箱
//相当于编译器自动为您作以下的语法编译：
int j = i.intValue();
```



​	**包装类的缓存问题**：

​		如果数据在-128~127这个区间，那么在类加载时就已经为该区间的每个数值创建了对象，并将这256个对象存放到一个名为cache的数组中。<u>每当自动装箱过程发生时(或者手动调用valueOf()时)，就会先判断数据是否在该区间，如果在则直接获取数组中对应的包装类对象的引用，如果不在该区间，则会通过new调用包装类的构造方法来创建对象。</u>（对于-128~127之间的值会进行缓存处理。超过范围后，对象之间不能再使用==进行数值的比较[这时为false]，而是使用equals方法）



##### <font color = "greenbule">DateFormat类和SimpleDateFormat类</font>

​	把**时间对象转化成指定格式的字符串**。反之，把指定格式的字符串转化成时间对象。

​    DateFormat是一个抽象类，一般使用它的的子类SimpleDateFormat类来实现。

```java
SimpleDateFormat s1 = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
SimpleDateFormat s2 = new SimpleDateFormat("yyyy-MM-dd");
        // 将时间对象转换成字符串
String daytime = s1.format(new Date());
System.out.println(daytime);
System.out.println(s2.format(new Date()));

// 将符合指定格式的字符串转成成时间对象.字符串格式需要和指定格式一致。
        String time = "2007-10-7";
        Date date = s2.parse(time);
        System.out.println("date1: " + date);
```

![表8-2 格式化字符的含义.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495609352295957.png)

```java
SimpleDateFormat s1 = new SimpleDateFormat("D");
String daytime = s1.format(new Date());
System.out.println(daytime);//打印输出137  当前年中的天数
```



##### Math类

​	1. abs 绝对值

   2. acos,asin,atan,cos,sin,tan 三角函数

3. sqrt 平方根

4. pow(double a, double b) a的b次幂

5. max(double a, double b) 取大值

   6. min(double a, double b) 取小值

7. ceil(double a) 大于a的最小整数

8. floor(double a) 小于a的最大整数

9. random() 返回 0.0 到 1.0 的随机数

10. long round(double a) double型的数据a转换为long型(四舍五入)

11. toDegrees(double angrad) 弧度->角度

12. toRadians(double angdeg) 角度->弧度



##### File类

​	<font color = "blue">不能读写文件内容</font>

​	![表8-3 File类访问属性的方法列表.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495611382530451.png)

![表8-4 File类创建文件或目录的方法列表.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495611400819053.png)



##### 枚举类

​	当需要定义一组常量时，可以使用枚举类型。



**容器**

​	数组的优势：是一种简单的线性序列，可以快速地访问数组元素，效率高。如果从效率和类型检查的角度讲，数组是最好的。

   数组的劣势：不灵活。容量需要事先定义好，不能随着需求的变化而扩容

![图9-1容器的接口层次结构图.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495613220648265.png)



### Collection类

​	Collection接口的两个<u>子接口</u>是List、Set接口。

​	![表9-1 Collection接口中定义的方法.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495614959696503.png)

   1.void sort(List) //对List容器内的元素排序，排序的规则是按照升序进行排序。

2. void shuffle(List) //对List容器内的元素进行随机排列。

3. void reverse(List) //对List容器内的元素进行逆续排列 。

4. void fill(List, Object) //用一个特定的对象重写整个List容器。

5. int binarySearch(List, Object)//对于顺序的List容器，采用折半查找的方法查找特定对象。



#### List列表

​	**List接口常用的实现类有3个：ArrayList、LinkedList和Vector。**

​	1、**有序**：List中每个元素都有索引标记。可以根据元素的索引标记(在List中的位置)访问元素，从而精确控制这些元素。

​	2、**可重复**

```Java
		List<String> list = new ArrayList<String>();
        System.out.println(list.isEmpty()); // true,容器里面没有元素
        list.add("高淇");
        System.out.println(list.isEmpty()); // false,容器里面有元素
        list.add("高小七");
        list.add("高小八");
        System.out.println(list);
        System.out.println("list的大小：" + list.size());
        System.out.println("是否包含指定元素：" + list.contains("高小七"));
        list.remove("高淇");
        System.out.println(list);
        Object[] objs = list.toArray();
        System.out.println("转化成Object数组：" + Arrays.toString(objs));
        list.clear();
        System.out.println("清空所有元素：" + list);
```

​	

##### ArrayList

​	查询效率高，增删效率低，线程不安全

​	**实现原理**：ArrayList的Object数组初始化长度为10，如果我们存储满了这个数组，需要存储第11个对象，就会定义新的长度更大的数组，并将原数组内容和新的元素一起加入到新数组中。

##### LinkedList

​	底层用双向链表实现的存储。特点：查询效率低，增删效率高，线程不安全。

##### Vector

​	线程安全，效率低

<font color = "blue">如何选用ArrayList、LinkedList、Vector?</font>

1. 需要线程安全时，用Vector。

2. 不存在线程安全问题时，并且<u>查找</u>较多用<u>ArrayList</u>(一般使用它)。

3. 不存在线程安全问题时，<u>增加或删除</u>元素较多用<u>LinkedList</u>。



#### Map映射

​	Map就是用来存储“键(key)-值(value) 对”的。 Map类中存储的“键值对”通过键来标识，所以“键对象”<u>**不能重复**</u>。

​	**Map 接口的实现类有HashMap、TreeMap、HashTable、Properties等。**

![表9-3 Map接口中常用的方法.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495617463792119.png)



##### HashMap

​	(key,value)键值对

​	无序；允许key或value为null；线程不安全，效率高；key不可重复，value可重复并替换

​	由于底层采用了哈希表存储数据，我们要求键不能重复，<u>如果发生重复，新的键值对会替换旧的键值对。</u>

​	<font color= "blue">**内部存储原理**</font>：当添加一个元素(key-value)时，首先计算key的hash值，以此确定插入数组中的位置，但是可能存在同一hash值的元素已经被放在数组同一位置了，这时就添加到同一hash值的元素的后面，他们在数组的同一位置，就形成了链表，同一个链表上的Hash值是相同的，所以说数组存放的是链表。 JDK8中，当链表长度大于8时，链表就转换为红黑树，这样又大大提高了查找的效率。

​	<font color= "blue">扩容问题</font>：HashMap的位桶数组，初始大小为16。实际使用时，显然大小是可变的。如果位桶数组中的元素达到(0.75*数组 length)， 就重新调整数组大小变为原来2倍大小。



##### HashTable

​	线程安全，效率低；**不允许key或value为空**（其余与HashMap一致）



#### Set集

​	特点：无序、不可重复、只能放入一个null元素



#### 迭代器

​	<u>Set\List迭代</u>

```java
List<String> list = new ArrayList<String>();//List迭代----set迭代类似
Iterator<String> list = new Iterator();
```

​	<u>Map迭代</u>

```java
//Map迭代需要用set集合来	获取key值的集合
Map<String, String> map = new HashMap<String, String>();

Set<String> ss = map.keySet();//set集合来获取key值的集合
for (Iterator<String> iterator = ss.iterator(); iterator.hasNext();) {
            String key = iterator.next();
            System.out.println(key + "--" + map.get(key));
        }


//方法二		使用entrySet			同时拿到key和value
Set<Entry<Integer, Man>>  ss = maps.entrySet();
for (Iterator iterator = ss.iterator(); iterator.hasNext();) {
    Entry e = (Entry) iterator.next(); 
    System.out.println(e.getKey()+"--"+e.getValue());
```



### I/O技术

​	**流**：是一连串连续动态的数据集合。

​	对于**输入流而言**，<font color = "blue">数据源就像水箱，流(stream)就像水管中流动着的水流</font>，程序就是我们最终的用户。我们通过流(A Stream)将数据源(Source)中的数据(information)输送到程序(Program)中。

   对于**输出流而言**，目标数据源就是目的地(dest)，我们通过流(A Stream)将程序(Program)中的数据(information)输送到目的数据源(dest)中。

```java 
		FileInputStream fis = null;
        try {
            fis = new FileInputStream("d:/a.txt"); // 内容是：abc
            StringBuilder sb = new StringBuilder();
            int temp = 0;
            //当temp等于-1时，表示已经到了文件结尾，停止读取
            while ((temp = fis.read()) != -1) {
                sb.append((char) temp);
            }
            System.out.println(sb);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                //这种写法，保证了即使遇到异常情况，也会关闭流对象。
                if (fis != null) {
                    fis.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```



1. **节点流**：可以直接从数据源或目的地读写数据，如FileInputStream、FileReader、DataInputStream等。

2. **处理流**：不直接连接到数据源或目的地，是”<u>处理流的流”。通过对其他流的处理提高程序的性能</u>，如BufferedInputStream、BufferedReader等。处理流也叫包装流。



#### 文件字节流

##### FileInputStream

​	通过字节的方式**读取文件**，适合读取所有类型的文件(图像、视频、文本文件等)。Java也提供了`FileReader`专门<u>读取文本文件</u>。

##### FileOutputStream 

​	通过字节的方式写数据到文件中，适合所有类型的文件。Java也提供了FileWriter专门写入文本文件。

```java
		FileOutputStream fos = null;
        String string = "北京尚学堂欢迎您！";
        try {
            // true表示内容会追加到文件末尾；false表示重写整个文件内容。
            fos = new FileOutputStream("d:/a.txt", true);
            //getBytes该方法是直接将一个字节数组写入文件中; 而write(int n)是写入一个字节
            fos.write(string.getBytes());
        }
```

[^附注]: 1. 为了减少对硬盘的读写次数，提高效率，通常设置缓存数组。相应地，读取时使用的方法为：read(byte[] b);写入时的方法为：write(byte[ ] b, int off, int length)。---------不加缓存数组，就会一个字节一个字节的读，太慢！！！



#### 文件字符流

​	<font color = "blue">处理文本文件(Unicode字符)</font>，一般可以使用文件字符流，它以<u>字符为单位</u>进行操作。

##### FileReader && FileWriter

```Java
		FileReader fr = null;
        FileWriter fw = null;
        int len = 0;
        try {
            fr = new FileReader("d:/a.txt");
            fw = new FileWriter("d:/d.txt");
            //为了提高效率，创建缓冲用的字符数组
            char[] buffer = new char[1024];
            //边读边写----------每次读的时候都读取1024字符
            while ((len = fr.read(buffer)) != -1) {
                fw.write(buffer, 0, len);
            }
        }
```



#### 缓冲字节流

​	当对文件或者其他数据源进行<u>频繁的读写</u>操作时，效率比较低，这时如果使用<u>缓冲流就能够更高效的读写信息</u>。因为缓冲流是先将数据缓存起来，然后当**缓存区存满后或者手动刷新时再一次性的读取到程序或写入目的地**。

##### BufferedInputStream && BufferedOutputStream

```java
		FileInputStream fis = null;
        BufferedInputStream bis = null;
        FileOutputStream fos = null;
        BufferedOutputStream bos = null;
        int temp = 0;
        try {
            fis = new FileInputStream(src);
            fos = new FileOutputStream(dec);
            //使用缓冲字节流包装文件字节流，增加缓冲功能，提高效率
            //缓存区的大小（缓存数组的长度）默认是8192，也可以自己指定大小
            bis = new BufferedInputStream(fis);
            bos = new BufferedOutputStream(fos);
            while ((temp = bis.read()) != -1) {
                bos.write(temp);
            }
        }//在关闭流时，应该先关闭最外层的包装流，即“后开的先关闭”。
```



#### 缓冲字符流

##### 	BufferedReader && BufferedWriter

​	处理文本时，我们一般可以使用缓冲字符流加快速度！

```java
// 注：处理文本文件时，实际开发中可以用如下写法，简单高效！！
        FileReader fr = null;
        FileWriter fw = null;
        BufferedReader br = null;
        BufferedWriter bw = null;
        String tempString = "";
        try {
            fr = new FileReader("d:/a.txt");
            fw = new FileWriter("d:/d.txt");
            //使用缓冲字符流进行包装
            br = new BufferedReader(fr);
            bw = new BufferedWriter(fw);
            //BufferedReader提供了更方便的readLine()方法，直接按行读取文本
            //br.readLine()方法的返回值是一个字符串对象，即文本中的一行内容，所以判断的时候判断为null
            while ((tempString = br.readLine()) != null) {
                //将读取的一行字符串写入文件中
                bw.write(tempString);
                //下次写入之前先newLine()换行，否则会在上一行后边继续追加，而不是另起一行
                bw.newLine();
            }
        }
```



#### 字节数组流

##### ByteArrayInputStream && ByteArrayOutputStream

​	<font color = "blue">用在需要流和数组之间转化的情况。</font>

​	<u>FileInputStream是把文件当做数据源。ByteArrayInputStream则是<font color = "blue">把内存中的”某个字节数组对象”当做数据源</font>。</u>

```java
public static void main(String[] args) {
        //将字符串转变成字节数组，然后作为数据源进行操作
        byte[] b = "abcdefg".getBytes();
        test(b);
    }
    public static void test(byte[] b) {
        ByteArrayInputStream bais = null;
        StringBuilder sb = new StringBuilder();
        int temp = 0;
        //用于保存读取的字节数量
        int num = 0; 
        try {
            //该构造方法的参数是一个字节数组，这个字节数组就是数据源
            bais = new ByteArrayInputStream(b);
            while ((temp = bais.read()) != -1) {
                sb.append((char) temp);
                num++;
            }
            System.out.println(sb);
            System.out.println("读取的字节数：" + num);
        } finally {
            try {
                if (bais != null) {
                    bais.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```



#### 数据流

​	将<font color = "blue">“基本数据类型与字符串类型int、double、String等”作为数据源</font>，从而允许程序以与机器无关的方式从底层输入输出流中操作Java基本数据类型与字符串类型。

##### DataInputStream && DataOutputStream

​	是处理流，可以对其他节点流或处理流进行包装，增加一些更灵活、更高效的功能。

```java
		DataOutputStream dos = null;
        DataInputStream dis = null;
        FileOutputStream fos = null;
        FileInputStream  fis = null;
        try {
            fos = new FileOutputStream("D:/data.txt");
            fis = new FileInputStream("D:/data.txt");
            //使用数据流对缓冲流进行包装，新增缓冲功能
            dos = new DataOutputStream(new BufferedOutputStream(fos));
            dis = new DataInputStream(new BufferedInputStream(fis));
            //将如下数据写入到文件中
            dos.writeChar('a');
            dos.writeInt(10);
            dos.writeDouble(Math.random());
            dos.writeBoolean(true);
            dos.writeUTF("北京尚学堂");
            //手动刷新缓冲区：将流中数据写入到文件中
            dos.flush();
            //直接读取数据：读取的顺序要与写入的顺序一致，否则不能正确读取数据。
            System.out.println("char: " + dis.readChar());
            System.out.println("int: " + dis.readInt());
            System.out.println("double: " + dis.readDouble());
            System.out.println("boolean: " + dis.readBoolean());
            System.out.println("String: " + dis.readUTF());
        } 
```



#### 对象流

​	对某个对象进行读写操作。以“对象”为数据源，但是<u>必须将传输的对象进行序列化与反序列化操作。</u>

​	对象流不仅可以读写对象，还可以读写基本数据类型。

##### ObjectInputStream && ObjectOutputStream

```java
// 创建Object输出流，并包装缓冲流，增加缓冲功能
----------//当准备将一个对象写入文件的时候，首先用OutputStream的子类创建一个输出流。！！！！！！！！<下面第一行>
        OutputStream os = null;			//首先需要创建输出流
        BufferedOutputStream bos = null;		//包装缓冲流
        ObjectOutputStream oos = null;			//对象流
        try {
            os = new FileOutputStream(new File("d:/bjsxt.txt"));
            bos = new BufferedOutputStream(os);
            oos = new ObjectOutputStream(bos);
            // 使用Object输出流
            //对象流也可以对基本数据类型进行读写操作
            oos.writeInt(12);
            oos.writeDouble(3.14);
            oos.writeChar('A');
            oos.writeBoolean(true);
            oos.writeUTF("北京尚学堂");
            //对象流能够对对象数据类型进行读写操作
            //Date是系统提供的类，已经实现了序列化接口
            //如果是自定义类，则需要自己实现序列化接口
            oos.writeObject(new Date());
        }catch (IOException e) {
            e.printStackTrace();
        } finally {
            //关闭输出流
            if(oos != null){
                try {
                    oos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(bos != null){
                try {
                    bos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if(os != null){
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
```



#### 转换流

##### InputStreamReader && OutputStreamWriter

​	用来实现将**字节流转化成字符流**。可以在一定程度上避免乱码。

```JAVA
// 创建字符输入和输出流:使用转换流将字节流转换成字符流
        BufferedReader br = null;
        BufferedWriter bw = null;
        try {
            br = new BufferedReader(new InputStreamReader(System.in));
            bw = new BufferedWriter(new OutputStreamWriter(System.out));
            // 使用字符输入和输出流
            String str = br.readLine();
            // 一直读取，直到用户输入了exit为止
            while (!"exit".equals(str)) {
                // 写到控制台
                bw.write(str);
                bw.newLine();// 写一行后换行
                bw.flush();// 手动刷新
                // 再读一行
                str = br.readLine();
            }
        }
```



#### 序列化和反序列化--对象流

​	当两个进程远程通信时，**<u>发送方</u>需要把这个Java对象转换为字节序列<font color = "blue">（序列化）</font>，才能在网络上传送;<u>接收方</u>则需要把字节序列再恢复为Java对象才能正常读取<font color = "blue">（反序列化）</font>。**

​	序列化：`ObjectOutputStream.writeObject()`

​	反序列化：`ObjectInputStream.readObject()`

​	只有实现了`Serializable`接口的类的对象才能被序列化。 `Serializable`接口是一个空接口，只起到标记作用。（序列化的类需要实现 Serializable 接口）

```java
//Person类实现Serializable接口后，Person对象才能被序列化
class Person implements Serializable {
    // 添加序列化ID，它决定着是否能够成功反序列化！
    private static final long serialVersionUID = 1L;
    int age;
    boolean isMan;
    String name;
 
    public Person(int age, boolean isMan, String name) {
        super();
        this.age = age;
        this.isMan = isMan;
        this.name = name;
    }
 
    @Override
    public String toString() {
        return "Person [age=" + age + ", isMan=" + isMan + ", name=" + name + "]";
    }
}
 
public class TestSerializable {
    public static void main(String[] args) {
        FileOutputStream fos = null;
        ObjectOutputStream oos = null;
        ObjectInputStream ois = null;
        FileInputStream fis = null;
        try {
            // 通过ObjectOutputStream将Person对象的数据写入到文件中，即序列化。
            Person person = new Person(18, true, "高淇");
            // 序列化
            fos = new FileOutputStream("d:/c.txt");
            oos = new ObjectOutputStream(fos);
            oos.writeObject(person);
            oos.flush();
            // 反序列化
            fis = new FileInputStream("d:/c.txt");
            // 通过ObjectInputStream将文件中二进制数据反序列化成Person对象：
            ois = new ObjectInputStream(fis);
            Person p = (Person) ois.readObject();
            System.out.println(p);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (oos != null) {
                try {
                    oos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fos != null) {
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (ois != null) {
                try {
                    ois.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



#### 多线程

​	**进程：**进程是程序的一次动态执行过程， 占用特定的地址空间；进程退出后则回收内存空间；进程间相互独立，只有通信时才有联系。

​	**线程：**1、多个线程共享相同的进程内存单元/内存地址空间，可以访问相同的变量和对象，而且它们从同一堆中分配对象并进行通信、数据交换和同步操作；

​				2、由于线程间的通信是在同一地址空间上进行的，所以<u>不需要额外的通信机制</u>，这就使得通信更简便而且信息传递的速度也更快。

​	**在java中所以的线程都是同时启动的，至于什么时候，哪个先执行，完全看谁先得到CPU的资源。**

​	**在java中，每次程序运行至少启动2个线程。一个是main线程，一个是垃圾收集线程。**

​	线程生命周期如下：

![图11-4 线程生命周期图.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170526/1495787690411518.png)



##### 一：通过Thread类实现多线程

​	不适合资源共享

```java
public class TestThread extends Thread {//自定义类继承Thread类
    //run()方法里是线程体
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(this.getName() + ":" + i);//getName()方法是返回线程名称
        }
    }
 
    public static void main(String[] args) {
        TestThread thread1 = new TestThread();//创建线程对象
        thread1.start();//通过调用Thread类的start()方法来启动一个线程。
        
        TestThread thread2 = new TestThread();
        thread2.start();
    }
}
```

##### 二：通过Runnable接口实现多线程

​	1、很容易的实现资源共享；

​	2、可以避免java中的单继承的限制（因为继承Thread类时不能多继承）；

​	3、线程池只能放入实现Runable或callable类线程，不能直接放入继承Thread的类

```Java
public class TestThread2 implements Runnable {//自定义类实现Runnable接口；
    //run()方法里是线程体；
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName() + ":" + i);
        }
    }
    public static void main(String[] args) {
        //创建线程对象，把实现了Runnable接口的对象作为参数传入；
        Thread thread1 = new Thread(new TestThread2());
        thread1.start();//启动线程；
        Thread thread2 = new Thread(new TestThread2());
        thread2.start();
    }
}//！！！！！！！--------------更加通用--------------！！！！！！！！！
```



##### 并发和并行

​	并发：物理上同时发生，指的是一个**<font color = "blue">时间内</font>同时运行多个程序(同一个时间段内都在做各自的任务)**；

​	并行：逻辑上同时发生，指的是一个**<font color = "blue">时间点</font>同时运行多个程序**； 



##### 线程同步

​	线程同步其实就是一种等待机制，多个需要同时访问此对象的线程进入这个对象的等待池形成队列，等待前面的线程使用完毕后，下一个线程再使用。



##### 死锁

​	多个线程各自占有一些共享资源，并且互相等待其他线程占有的资源才能进行，而导致两个或者多个线程都在等待对方释放资源，都停止执行的情形。