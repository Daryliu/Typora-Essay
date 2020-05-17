### ArrayList

##### 与数组的区别

数组可能会导致浪费或溢出；

ArrayList容器类的容量会随着对象的增加自动增长。

```java
public class TestCollection {
    @SuppressWarnings("rawtypes")
    public static void main(String[] args) {
        //容器类ArrayList，用于存放对象
        ArrayList heros = new ArrayList();
        //添加对象
        heros.add( new Hero("盖伦"));
        System.out.println(heros.size());//打印ArrayList中对象的个数  1
         
        //容器的容量"capacity"会随着对象的增加，自动增长
        //只需要不断往容器里增加英雄即可，不用担心会出现数组的边界问题。
        heros.add( new Hero("提莫"));
        System.out.println(heros.size());  //2个对象
    }
```

##### list接口

ArrayList实现了接口List，常见的写法会把引用声明为接口List类型；

```java
public class TestCollection {
    public static void main(String[] args) {
        //ArrayList实现了接口List
         
        //常见的写法会把引用声明为接口List类型，为什么一般都使用 List list = new ArrayList() ,而不用 ArrayList alist = new ArrayList()呢？ 问题就在于List有多个实现类，如 LinkedList或者Vector等等
        List heros = new ArrayList();//接口引用指向子类对象（多态）
        heros.add( new Hero("盖伦"));
        System.out.println(heros.size()); 
    }   
}
```

##### 泛型 Generic

不指定泛型的容器，可以存放任何类型的元素；

指定了泛型的容器，只能存放指定类型的元素以及其子类。

```java
public static void main(String[] args) {
          
        //对于不使用泛型的容器，可以往里面放英雄，也可以往里面放物品
        List heros = new ArrayList(); 
        heros.add(new Hero("盖伦"));
        //本来用于存放英雄的容器，现在也可以存放物品了
        heros.add(new Item("冰杖")); 
        //对象转型会出现问题
        Hero h1=  (Hero) heros.get(0);
        //尤其是在容器里放的对象太多的时候，就记不清楚哪个位置放的是哪种类型的对象了
        Hero h2=  (Hero) heros.get(1);
          
    
        //引入泛型Generic
        //声明容器的时候，就指定了这种容器，只能放Hero，放其他的就会出错
        List<Hero> genericheros = new ArrayList<Hero>();
        genericheros.add(new Hero("盖伦"));
        //如果不是Hero类型，根本就放不进去
        //genericheros.add(new Item("冰杖"));
          
        //除此之外，还能存放Hero的子类
        genericheros.add(new APHero()); 
        //并且在取出数据的时候，不需要再进行转型了，因为里面肯定是放的Hero或者其子类
        Hero h = genericheros.get(0); 
    }
```

简写的泛型：

```java
List<Hero> genericheros = new ArrayList<Hero>();=====>List<Hero> genericheros = new ArrayList<>();//后面的类型可不写
```

#### 遍历ArrayList的三种方法

##### 用for循环遍历

```java
for (int i = 0; i < heros.size(); i++) {
            Hero h = heros.get(i);//获取位置
            System.out.println(h);
        }
```

##### 迭代器遍历

![806](C:\Users\daryl\Desktop\806.png)

```java
List<Hero> heros = new ArrayList<Hero>();
         
        //放5个Hero进入容器
        for (int i = 0; i < 5; i++) {
            heros.add(new Hero("hero name " +i));
        }
         
        //第二种遍历，使用迭代器
        System.out.println("--------使用while的iterator-------");
        Iterator<Hero> it= heros.iterator();
        //从最开始的位置判断"下一个"位置是否有数据
        //如果有就通过next取出来，并且把指针向下移动
        //直到"下一个"位置没有数据
        while(it.hasNext()){
            Hero h = it.next();
            System.out.println(h);
        }
        //迭代器的for写法
        System.out.println("--------使用for的iterator-------");
        for (Iterator<Hero> iterator = heros.iterator(); iterator.hasNext();) {
            Hero hero = (Hero) iterator.next();
            System.out.println(hero);
        }
```

##### 用增强型for循环

```java
List<Hero> heros = new ArrayList<Hero>();
 
        // 放5个Hero进入容器
        for (int i = 0; i < 5; i++) {
            heros.add(new Hero("hero name " + i));
        }
 
        // 第三种，增强型for循环
        System.out.println("--------增强型for循环-------");
        for (Hero h : heros) {
            System.out.println(h);
        }
```

### 其他集合

##### LinkedList

与[ArrayList](https://how2j.cn/k/collection/collection-arraylist-method/685.html)一样，LinkedList也实现了List接口，诸如add,remove,contains等等方法

<u>除了实现了List接口外，LinkedList还实现了**双向链表结构**Deque，可以很方便的在头尾插入删除数据</u>

```java
//LinkedList是一个双向链表结构的list
        LinkedList<Hero> ll =new LinkedList<Hero>();
         
        //所以可以很方便的在头部和尾部插入数据
        //在"最后"插入新的英雄			addLast()
        ll.addLast(new Hero("hero3"));
        System.out.println(ll);
         
        //在"最前面"插入新的英雄			addFirst()
        ll.addFirst(new Hero("heroX"));
        System.out.println(ll);
         
        //查看最前面的英雄		getFirst()
        System.out.println(ll.getFirst());
        //查看最后面的英雄		getLast()
        System.out.println(ll.getLast());
         
        //查看不会导致英雄被删除
        System.out.println(ll);
        //取出最前面的英雄		removeFirst()
        System.out.println(ll.removeFirst());
         
        //取出最后面的英雄		removeLast()
        System.out.println(ll.removeLast());
         
        //取出会导致英雄被删除
        System.out.println(ll);
```

