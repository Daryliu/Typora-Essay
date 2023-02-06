# 集合

定义：存放于java.util包中，是一个用来<u>存放对象</u>的容器。主要包含Set（无序、不可重复的集合）、List（有序、可重复的集合）、Map（具有映射关系的集合）

1、只能存放对象（基本数据类int等会被自动转化为封装类Integer等）

2、集合存放的是多个<u>对象的引用</u>，对象本身还放在堆内存中

3、集合可以存放不同类型，不限数量的数据类型

---

#### HashSet

特点：不能保证元素的排列顺序、不可重复、不是线程安全的、集合元素可以使用null

当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据hashCode值决定其在HashSet中的存放位置。（也就是存放的位置和hashcode值有关，而且hashCode不相等就是不重复）

```java
//HashCode实现Set接口,Set接口继承Collection
Set set = new HashSet();

Iterator it = set.iterator();//1  使用迭代器遍历集合
while(it.hasNext()){
    System.out.println(it.next());
}

for(Object obj : set){//2  for each遍历集合		”更简单“
    System.out.println(obj);
}
***********************************************************************************
    //若想让集合只能存放相同类型的对象，需要使用“泛型”
    Set<String> set = new HashSet<String>();//<String>只能存String类型的对象

```

HashSet判断相等：对象的equals()比较之后相等，而且hashCode()的值相等。

---

#### TreeSet

是SortedSet接口的实现类；<font color="yellow"><u>有序(自然排序)</u></font>

