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

​	![表8-3 File类访问属性的方法列表.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495611382530451.png)

![表8-4 File类创建文件或目录的方法列表.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495611400819053.png)



##### 枚举类

​	当需要定义一组常量时，可以使用枚举类型。



**容器**

​	数组的优势：是一种简单的线性序列，可以快速地访问数组元素，效率高。如果从效率和类型检查的角度讲，数组是最好的。

   数组的劣势：不灵活。容量需要事先定义好，不能随着需求的变化而扩容

![图9-1容器的接口层次结构图.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495613220648265.png)



##### Collection接口

​	Collection接口的两个<u>子接口</u>是List、Set接口。

​	![表9-1 Collection接口中定义的方法.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495614959696503.png)



#### List

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



##### Map接口

​	Map就是用来存储“键(key)-值(value) 对”的。 Map类中存储的“键值对”通过键来标识，所以“键对象”<u>**不能重复**</u>。

​	**Map 接口的实现类有HashMap、TreeMap、HashTable、Properties等。**

![表9-3 Map接口中常用的方法.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170524/1495617463792119.png)



##### HashMap

​	(key,value)键值对

​	无序；允许key或value为null；线程不安全，效率高

​	由于底层采用了哈希表存储数据，我们要求键不能重复，<u>如果发生重复，新的键值对会替换旧的键值对。</u>



##### HashTable

​	线程安全，效率低；**不允许key或value为空**（其余与HashMap一致）