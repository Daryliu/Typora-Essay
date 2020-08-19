实际cmd中执行需要javac XXX.java然后java  XXX
1.类class   
实例instance（根据class创建的实例，可以创建多个instance，每个instance类型相同，但各自属性可能不相同）
人     类/class     class Person{}
小明   实例/ming    （定义person类型的变量ming）
Person ming = new Person()→ 创建实例并通过ming指向它
小红   实例/hong    Person hong = new Person()


一个class可包含多个field（字段）
class Person{
	public String name;//程序数据不安全，未封装
	public int age;
}

2.方法：
当字段从public改成private，外部代码不可访问，则需要方法（method）来让外部代码间接修改字段
可以通过getName()和getAge()间接获取private字段的值
在方法内部，可以使用一个隐含的变量this，它始终指向当前实例。因此，通过this.field就可以访问当前实例的字段
class Person {
    private String name;
    public String getName() {
        return name; // 相当于this.name
    }
}
若有局部变量和字段重名，那么局部变量优先级更高，就必须加上this
class Person {
    private String name;

    public void setName(String name) {
        this.name = name; // 前面的this不可少，少了就变成局部变量name了
		//前面的this   name为声明的，后面的是方法本身的
    }
}

若为数组则调用方需要自己先构造String[]
Group g = new Group();
g.setNames(new String[] {"Xiao Ming", "Xiao Hong", "Xiao Jun"}); // 传入1个String[]

3.参数绑定：
1.基本类型参数的传递，是调用方值的复制。双方各自的后续修改，互不影响
2.引用类型参数的传递，调用方的变量，和接收方的参数变量，指向的是同一个对象。
双方任意一方对这个对象的修改，都会影响对方（因为指向同一个对象嘛）
public static void main(String[] args) {
        Person p = new Person();
        String[] fullname = new String[] { "Homer", "Simpson" };
        p.setName(fullname); // 传入fullname数组
        System.out.println(p.getName()); // "Homer Simpson"
        fullname[0] = "Bart"; // fullname数组的第一个元素修改为"Bart"
        System.out.println(p.getName()); // "Bart Simpson" 	
		
		
		
4.构造方法/构造器：（没有返回值声明，但是会返回返回值，但是return不需要写，返回值类型是构造方法所在类的类型，即类本身）  
			在创建对象实例时就把内部字段全部初始化为合适的值
		1)构造方法名和当前类名一致（存在的意义就是创建对象）   无参的构造方法由系统创建
		2)构造方法调用需要new   例如new Person(实参列表);
		User u = new User();这里的User是构造方法，先执行后面的new构造方法
		3)构造方法支持重载机制Overload
		4)创建对象的同时初始化实例变量的内存空间（构造方法执行过程当中new开辟并进行赋值）,系统默认赋值     
例：class Person{
	public Persion(){
		//构造方法
	}
}
Note：先初始化赋值再用构造方法初始化则值为构造方法赋的值。
	5)构造方法可调用其他构造方法
		public int date(int year,int month,int day){
			this.year = year;
			this.month = month;
			this.day = day;
		}
		public Date()//构造
		{		
		this(1970,1,1);//不会再创建对象!!   this(实参)
		}


5.方法重载(Overlord)：同一个类中，方法名相同，但各自的参数不同
	1>public void hello(String name, int age)
	2>public void hello(String name)
	3>public void hello()     功能相似的时候尽可能让方法名相同
	
	
	方法递归：（调用方法本身，及其耗费内存）需要设置结束条件，否则出现栈溢出

6.继承(extends)：代码复用，有了继承才有了多态和方法的覆盖 （java只支持单继承，可间接继承，C++支持多继承）   
		有就近原则
！！！私有类的不支持继承；构造方法不支持继承；其他数据都可以继承！！！
   
			Person可称为超类、父类、基类   super关键字表示父类（超类）。子类引用父类的字段时，可以用super.fieldName 
			super(); // 自动调用父类的构造方法
			Student称为子类、扩展类，派生类
			student →→→→→→→→Person→→→→→→→→Object（所有类默认继承）
	class Person {
    private String name;
    private int age;

    public String getName() {...}
    public void setName(String name) {...}
    public int getAge() {...}
    public void setAge(int age) {...}
	}
	
	[修饰符列表] class Student extends Person            学生类继承Person类
	{
    // 不要重复name和age字段/方法,
    // 只需要定义新增score字段/方法:
    private int score;

    public int getScore() { … }
    public void setScore(int score) { … }
	}

7.多态：允许添加更多类型的子类实现功能扩展，却不需要修改基于父类的代码（就是子类中添加了父类中不存在的方法）
		
	7.1覆写|重写（Override）：继承关系中，子类如果定义了一个与父类 方法签名完全相同的方法，被称为覆写（Override）
	（！！当父类中方法不能满足当前子类的业务需求，子类需要将继承的父类进行重写）
	（！！方法重写发生在具有继承关系的父子类中，方法名相同，返回值类型相同，参数形参列表相同，所以建议方法重载时尽量复制粘贴）
	 (！！访问权限不能更低，即public不能换private；抛出异常不能更多，可以更少)
	class Person {
    public void run() {
        System.out.println("Person.run");
    }
	}
	class Student extends Person {
    @Override//覆写    加上 @Override 可以让编译器帮助检查是否进行了正确的覆写

    public void run() {    //和93行代码相同
        System.out.println("Student.run");//与父类完全一样，但是内容可以修改，
    }
	}
	Note：私有方法/构造方法不能继承，所以不能覆写；静态方法不存在覆写
	覆写只针对方法，不谈变量和属性
	
	7.2多态 
		7.2.1 向上转型(子转父类型，也叫自动类型转换)
		7.2.2 向下转型(父转子。也叫强制类型转换，要有强制类型转换符)，（父转子，加子的类型）
		！！！当调用的方法是子类型中特有的父类型中不存在，需要向下转型 ！！
		 Cat c2 = （Cat）a2;
		向上和向下转型都需要有继承关系，否则编译报错
		!!!!！！！(多态)3父类型引用指向子类型对象！！！！！！！！
		例如：Animal a = new cat();（a是个引用，类型是Animal，若没有cat中的方法，则转换为cat类型）真实创建的cat对象，所以调用cat的方法
	
	多态在实际开发中的作用：降低程序中的耦合度
	
	
	类的多态中要满足父类引用指向子类对象，调用的方法有重写
	
	
	
8.封装：1)看不到事物复杂的一面，只留一个简单的操作窗口
		2)封装之后才会形成真正的对象，真正的独立体
		3)意味着以后的程序可重复使用，且对于事物本身更加安全
	封装的步骤：
				1)所有属性私有化，使用private进行修饰，只能在本类中访问
				2)对外提供简单的操作入口（读取属性的值get方法，修改属性的值set方法或赋值（可在set和get方法中进行安全控制，比如判断语句）
				3）public void set+属性首字母大写  
				比如：
				private int age；
				public void setAge(int a)
				{age = a；}
				public void get+属性首字母大写  比如：public void getAge(){return age；}
				
		4)  1有static修饰的方法调用   类名.方法名(实参);    上下文中没有当前对象，是通过类访问
			2没有static修饰的方法调用   (创建一个对象来调用)引用.方法名(实参)/变量; 
			3没有static的方法被称为“实例方法”
			4没有static的变量被称为“实例变量”
			
	！！Note：当一个行为/动作执行过程中是需要对象参与的，一定要定义为实例方法，不能带static关键字
	

9.对象和引用
			对象：使用new运算符在堆中开辟的内存空间
			引用：是一个变量
	1)java参数传递：传递的是变量中保存的值（有时候是字面值；有时候若形参是java对象在内存中的地址，则传递地址）
	例：User u = 0x1234;
	User  u2 = u;//u传递给u2实际上是将0x1234赋值给u2，u和u2是不同的局部变量；但是他们指向堆内存中同一个java对象




10. this 关键字 ( 1是一个引用，出现在实例方法中，存放在JVM堆内存java对象内部中，指向对象本身)   
				2代表当前正在执行的行为的对象！！！
				3不能出现在有static的方法当中，因为其中没有对象
		4)this不可省略：用于区分局部变量和实例变量的时候
		private int  id;
		public void setID(int id)
		{
			this.id = id;(this.id是实例变量的id，后面的id是局部变量的id)
		}
		5.1)可以使用在实例方法中，代表当前对象【this.】
		5.1)可以使用在构造方法中，通过当前的构造方法调用其他的构造方法【this(实参)】
		public int date(int year,int month,int day){
			this.year = year;
			this.month = month;
			this.day = day;
		}
		public Date()//构造
		{	
		！！此语句只可出现在第一行，即调用构造器
		this(1970,1,1);//不会再创建对象!!   this(实参)
		}
	

	
11. static 关键字  表示静态变量(在类加载的时候初始化，不需要创建对象内存就已开辟了)，是类级别的，不加则是对象级别的；
		1)存储在方法区内存中
		2)所有对象都有这个属性，并且这个属性的值始终是一样的，此时要是用static
		3)所有对象都有这个属性，但是属性的值会随着对象的变化而变化，则使用实例变量（即没有static）
		4)可以使用其定义“静态代码块”，在 类加载时执行 且只执行 一次，系统加载该类之时立即执行这部分代码
		static{  !!静态代码块可用于完成日志的记录，是为程序员在类加载时刻用
		system.out.println("");
		}
		5)“实例代码块”即上面不加static，很少用，在构造方法之前执行
		6)静态方法：执行时产生的结果一样，已可以上升到类级别的动作，模板级别的动作（无法直接访问实例变量和实例方法，因为没有对象）。
		大多数工具类使用静态方法



12 final 关键字
		1)其修饰的类 无法被继承
		2)修饰的方法无法被覆盖，不可被重写
		3)修饰的变量一旦被赋值，无法重新赋值
		4)修饰引用时，表示该引用只有1次机会 指向对象
		5)
		6)


异常：1.类型转换异常classCastException
		使用 Instanceof 运算符可避免这种错误（强制类型转换前使用），其运行结果是布尔类型(true/false)。true表示a这个引用指向的对象是一个animla类型
		instanceof判读一个引用所指向的对象，例可判断所指向的是否是hero类型或hero的子类
		Animal a3 = new Bird();
		if(a3 instanceof Cat){//a3是一个cat类型的对象
			Cat c3 = (Cat)a3;
			c3.catchMouse();
		}else if(a3 instanceof Bird){
			Bird b2 = (Bird)a3;
			b2.fly();
		}
		
		
		
		
		try(FileInputStream readfile = new FileInputStream("read.txt");
            InputStreamReader ir = new InputStreamReader(readfile);
            BufferedReader in = new BufferedReader(ir))
			自动资源管理  try后面加()里面有多个;隔离用于初始化或声明
		
	



13面向对象内存分析：
栈stack 方法执行的内存模型，JVM为每个线程创建一个栈，每个方法执行会创建一个栈帧。线程私有，后进先出

堆heap 存储对象和数组   jvm中只有一个堆，被所有线程共享，是不连续的内存空间
方法区method area jvm中只有一个方法区，被所有线程共享，同来存放永远不变的内容，例如class类对象，静态变量，字符串常量


14垃圾回收机制(了解，有JVM调用)   finalize（）当对象没有任何引用指向的时候，就满足垃圾回收机制
	发现无用的对象；引用计数法，引用可达法(没有被到达的对象即可回收)
	清理占用的内存；
	分代的垃圾回收机制：年轻代Eden，年老代(经过多次回收依然存在)，持久代
	
	

15 super 关键字	







16内部类和抽象类、嵌套类、接口 
	object中tostring方法是返回当前对象的字符串表达
	16.1抽象类：解决不确定行为的方法定义，只能用于处理符合条件的特定要求
	抽象方法public abstract void attack(); 凡是被abstract修饰的抽象方法，由于方法的具体实现并不明确，因此抽象方法没有花括号所包裹着的方法体， 其子类会被要求实现attack方法，
	抽象类不可创建其实例，因为其是一个尚未完工的类
	抽象类可以定义public,protected,package,private,静态和非静态属性  final和非final属性
	
	16.11接口 中声明的属性只能是public 静态的 final （可加可不加） 的，没有构造方法，可继承多个接口
	 接口内部只能是抽象方法，所以接口内部方法的 abstract 前缀可加可不加，即使不加abstract，编译器也会默认把该方法当作抽象方法
	 
	
	扩展接口：
	 继承了接口的类需要重写接口中的所有方法（Java8之前） 之后增加了默认方法，带有default前缀标识，接口内部需要编写默认方法的完整实现代码，实现类无需重写该方法即可直接继承使用
	 不支持静态成员（包括静态属性和静态方法）（Java8之前）增加了静态属性和静态方法，而且都通过前缀static来标识
	
	
	
	
	16.2内部类（在一个类的内部再定义另外一个类，仿佛新类是已有类的成员一般） 
	public class Tree {
	private String tree_name;
	
	public Tree(String tree_name) {
		this.tree_name = tree_name;
	}
	
	public void sprout() {
		System.out.println(tree_name+"发芽啦");
		// 外部类访问它的内部类，就像访问其它类一样，都要先创建类的实例，再访问它的成员
		Flower flower = new Flower("花朵");
		flower.bloom();
	}
	
	// Flower类位于Tree类的内部，它是个内部类
	public class Flower {
		private String flower_name;

		public Flower(String flower_name) {
			this.flower_name = flower_name;
		}

		public void bloom() {
			System.out.println(flower_name+"开花啦");
		}
	}
}
	其他类调用内部类的语法： new 外部类().new 内部类()  
	例如：TreeInner inner = new TreeInner("桃树");
	// 创建一个内部类的实例，需要在new之前添加“外层类的实例名.”
	TreeInner.Flower flower = inner.new Flower("桃花");要以定义完的inner来实例化内部实例
	
	
	静态内部类水晶类的实例化 不需要一个外部类的实例为基础，可以直接实例化 new 外部类.静态内部类();
	静态内部类不可以访问外部类的非静态成员
	
	/ 内部类里面的this关键字指代内部类自身
	System.out.println(this.tree_name+"的"+flower_name+"开花啦");
	/要想在内部类里面访问外部类的成员，就必须在this之前添加“外部类的名称.”
	
	16.2.1匿名内部类： 只适合运行一次的情况
	new 接口名称(参数){
			//实现接口声明的抽象方法
	}
	
	16.3嵌套类 在内部类的定义代码前面添加关键字static，表示这是一种静态的内部类，它无需强制绑定外部类的实例即可正常使用。
	嵌套类不能直接访问外层类的成员
	
	
17递归头  什么时候不调用自身方法
	递归体   什么时候需要用自身方法
	递归会占用大量系统的资源，内存消耗多，比循环慢，高性能时，尽量避免使用递归
	
	一个java文件只可有一个public修饰的类
	
	17.1foreach在遍历数组和集合上很方便，是for循环的简化版，
			格式：for（元素类型type  元素变量value  ： 遍历对象obj）{
				引用x的java语句；
			}
			例子：for(int x : arr){
				system.out.println(x+" ");
			}
	17.2 类名::方法名
		
		
18利用正则串分割字符串
		String类型提供了split方法用于切割字符串，把分隔符作为输入参数，该方法即可返回分割好的字符串数组
		其中.号和|号需要转义字符\处理作为参数，加号（+）、星号（）、横线（-），在正则串中均需进行转义，除法的斜杆符（/）、取余数的百分号（%）无需转义处理
		加减乘除符号之间通过竖线隔开，split("\\+|\\*|\\-|/|%")
		//通过逗号分割字符串
		private static void splitByComma() {
		String commaStr = "123,456,789";
		// 利用split方法指定按照逗号切割字符串
		String[] commaArray = commaStr.split(",");
		for (String item : commaArray) {
			System.out.println("comma item = "+item);
			}
		}
		
		18.1正则表达式校验
		matches方法中判断字符串是否符合正则条件
		
		
19.枚举
	public enum Season{
		SPRING,SUMMER,AUTUMN,WINTER
	}// 四个枚举项既是常量，又都属于Season类型，外部访问它们的格式为“Season.枚举项的名称”
	
20.Lambda表达式：（闭包）
	优化了匿名内部类，是个没有名字的方法（匿名方法），但方法体的内部代码是完整的
	1.调用匿名方法的地方，本身必须知晓该位置的参数类型
	2.参数类型必须是某个接口，并且该接口仅声明了一个抽象方法
	Arrays.sort(intArray, (Integer o1, Integer o2) -> {
	return Integer.compare(o2, o1); // 上面的箭头即是其标志
	});  匿名方法的参数列表---->匿名方法的方法体
	3.1Lambda表达式第一招。去掉了new、接口名称、方法名称
	3.2Lambda表达式第二招。去掉了输入参数的变量类型
	3.3Lambda表达式第三招。去掉了方法体的花括号，以及方法返回的return和分号 Arrays.sort(intArray, (o1, o2) -> Integer.compare(o2, o1));


21.函数式接口及方法引用
	函数式编程：函数本身作为另一个函数的参数或返回值
	1//定义一个行为接口，给动物类调用
	public interface Behavior {

		// 声明一个名叫行动的抽象方法
		public void act();
	}
	
	#########
	public class Animal {
		// 定义一个半夜行动的方法。具体的动作由输入行为的act方法执行
		public void midnight(Behavior behavior) {
		behavior.act();
		}
	}
	
	########
	private static void testCock() {
	Animal cock = new Animal("公鸡");
	// 调用midnight方法时，传入匿名内部类的实例
	cock.midnight(new Behavior() {
		@Override
		public void act() {
			System.out.println(cock.getName()+"在叫啦。");
		}
	});
	}


	2方法引用：  变量类型::该变量调用的方法名称
	resultArray = StringUtil.select(strArray, (str) -> str.isEmpty());等于resultArray = StringUtil.select(strArray, String::isEmpty);
	
	2.1静态方法和静态变量的引用   
	静态方法不能访问非静态的实例成员变量；局部变量不管是否显示的使用final继续是，都不能用lambda表达式修改
	“实例名称::方法名称”的引用形式，正是方法引用的第三个分支——实例方法引用
	private static void testInstance() {
	MathUtil math = new MathUtil();
	double result;
	// 双冒号的方法引用也适用于实例方法，求两数之和
	result = Arithmetic.calculate(math::add, 3, 2);}
	
	
22.集合HashSet和TreeSet（统称为容器，还包含映射map，清单list，集合set等）    无序无重复元素
			###集合居然不提供get方法，无法取出数据#### 实际开发很少使用！！
		22.1集合：同类聚集，里面的每个事物都是唯一的，即使重复加入也只算同一个元素，区别是有序还是无序
			Set<数据类型> 变量;不可直接创建集合对象，相当于接口；因此采用HashSet（哈希集合，其内部采取哈希表来存储数据）和TreeSet（二叉集合，其内部采取二叉树来存储数据）
				
				HashSet<String> set = new HashSet<String>();集合实例    
				
					有set.add;.remove;.contains判断包含;.clear清空;.isEmpty;.size获取集合的大小
				集合上的遍历：  1for循环遍历、
								2迭代器遍历（初始的迭代器指向集合的存储地址） 
								Iterator<String> iterator = list.iterator();
								while(list.hasNext()){
									Object o = list,next();
									String s= (String) o;
								}
								调用集合实例的iterator方法即可获得该集合的迭代器
								3forEach遍历
								
23.映射：HashMap和TreeMap      
		通过 键名Key、键值Value 来对应
		Map<Key，Value> 	Key表示键名的数据类型，Value表示键值的数据类型
				23.1创建映射实例必须同时指定键名和键值的数据类型
					HashMap<String, MobilePhone> map = new HashMap<String, MobilePhone>();//MobilePhone为设置的数据类型
				23.2往映射中添加新的键值对，调用的是put方法，并且put方法的第一个参数为新元素的键名，第二个参数为新元素的键值
					map.put("米8", new MobilePhone("小米", 3000));//“米8”为键名
				23.3遍历	迭代器遍历：
							Set<Map.Entry<String, MobilePhone>> entry_set = map.entrySet();/调用映射实例的entrySet获得该映射的集合入口
							Iterator<Map.Entry<String, MobilePhone>> iterator = entry_set.iterator();/再调用入口对象的iterator方法获得映射的迭代器，然后使用迭代器遍历整个映射
						for循环遍历
						键名集合遍历：先调用映射的keySet方法获得只包含键名的集合，再通过遍历键名集合来获取每个键名对应的键值
							Set<String> key_set = map.keySet();
								for (String key : key_set)
						forEach方法遍历：map.forEach((key, value) ->
	

24.（有序可重复）清单：ArrayList（优先考虑，访问速度快）和LinkedList(双端队列，有序链表，批量插入和删除优)    有序集合，按照元素的先后顺序存放，可重复出现
			###  <> 中表示数据类型
		列表允许动态添加新元素
		列表不但支持修改指定位置的元素（set方法），还支持在指定位置插入新元素（add方法），或者移除指定位置的元素（remove方法）。
		简化的for循环  for (MobilePhone for_item : list)
		迭代器遍历  Iterator<String> iterator = list.iterator();与集合迭代器相同     list.toArray可转换为数组Array，Arrays.asList可转换为只读的list对象
		索引遍历   列表通过get方法获取指定位置的元素，而数组通过方括号引用某个下标
		for (int i = 0; i < list.size(); i++) {						
			MobilePhone index_item = list.get(i);
		forEach遍历 list.forEach(each_item -> System.out.println();
		
		
		indexOf(object o)从前往后查找集合元素，返回第一次出现的索引,不包含则返回-1
		subList(int fromIndex,int toIndex)返回两者之间的元素集合，值为List集合
	

25.泛型：表面意思是空泛的类型，也就是不明确的类型，要用的时候指定，含义即是模板    
				###若<>中为?，即泛指所有类型
			例如：ArrayList<T>  不需对类型进行强制转化，可以向上转型，即从ArrayList<Integer>向上转型为List<Integer>,T不可变
			ArrayList<String> strlist = new ArrayList<String>(); ###后面的系统可以识别出来，因此可以不写后面的String
		
		25.1编写泛型
				泛型类型不可再静态方法和静态字段后
				泛型还可以是多种类型  piar<String,Interger>
				
		25.2擦拭法
				编译器将T自动转换为Object，实现安全的强制转型，可能会产生重名
				T不可为基本类型
				无法获取带泛型的class，即无法实现getClass
				不能判断带泛型类型的类型  如：x instanceof Pair<String>
				
				
		25.3extends通配符
				
				Pair<? extends Number>
				###若?换为T，则只能add Number的子类，不可为String等
				则可以使用PairHelper.add(new Pair<int>(1,2));
				        和PairHelper.add(new Pair<double>(1.2,2.3)); 
						
						无法使用set方法；可以使用get方法获取number引用
						
						
		25.4super通配符
				Pair<? super Number>
					###若?换为T，则只能 Number的超类，不可为String等
					Integer i = p.getFirst();错误，只能确定是integer的超类，不能确定是integer
					PairHelper.add(new Pair<int>(1,2));
				    PairHelper.add(new Pair<double>(1.2,2.3)); 
					
					无法使用get方法；可以使用set方法获取number引用

		25.5泛型数组
				不能直接声明带泛型的数组Pair<String>[] ps = new Pair[2]；错误
				可以Pair<String>[] ps = (Pair<String>[]) new Pair[2];强制转型实现带泛型的数组
				
				不能直接创建T类型的数组，因为擦拭后会变成Object，必须借助Class<T>
				




26.IO输入输出
			IO流：单向流动，以byte为最小单位(字节流)，使用InputStream和OutputStream输入输出
			ASCII码位字符流，可使用Reader和Writer输入输出，也可输出文本
			
			同步IO：读写时要等数据返回后再执行下一步，代码编写简单，但是CPU执行效率低，例如java.io
			异步IO：读写IO仅发出请求，然后立即执行后续代码  例如：java.nio
			
			
			26.1文件系统File
			
			26.2InputStream是所有输入流的超类  读取字节  FileInputStream实现了文件流输入
			26.3OutputStream是输出流的超类   void flush();将缓冲区的内容输出
			OutputStream output = new FileOutputStream("文件路径或名称");
			output.write(21);//写入
			26.4















			
			
	

	
	
	
	