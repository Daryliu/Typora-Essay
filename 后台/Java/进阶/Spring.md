# Spring

---

一笔好字不错，二等才情不露，三斤酒量不醉，四季衣服不当，五子围棋

不悔，六出昆曲不推，七字歪诗不迟，八张马吊不查，九品头衔不选，

十分和气不俗。

**idea崩溃可全局搜idea.bat。在最后添加pause然后运行，查找错误原因。**

中文网站:https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference/overview.html#overview-spring

> 控制反转(IOC)、面向切面编程（AOP）
>
> 支持事务的处理，对框架的整合

##### Spring的组成

1. Spring Core
2. Spring AOP
3. Spring DAO
4. Spring
5. Spring
6. Spring
7. Spring MVC

##### 拓展

###### Spring Boot

- 快速开发的脚手架
- 基于SpringBoot可以快速开发单个微服务
- 约定大于配置

###### Spring Cloud

- 基于Spring Boot实现的



### 1、IOC控制反转

#### 1.1浅显易懂的讲解

**假设一个场景：**目前有三个角色，买水果的人（用户），卖水果的人（业务层），水果（持久化层），



![img](https://pic3.zhimg.com/50/v2-00e41ad1cf13fa3ef0cc5e4f990a239c_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-00e41ad1cf13fa3ef0cc5e4f990a239c_720w.jpg?source=1940ef5c)



先写一个接口，

```java
public interface Fruit {
    public void get();
}
```

现在实现3种水果的类，为了方便展示，把它们先写在一起，

```java
// Apple.java
public class Apple implements Fruit{
    public void get() {
        System.out.println("get an apple");
    }
}

// Orange.java
public class Banana implements Fruit{
    public void get() {
        System.out.println("get a banana");
    }
}

// Banana.java
public class Orange implements Fruit{
    public void get() {
        System.out.println("get a organe");
    }
}
```

现在实现一个业务层，也就是从3个水果类中获取水果，

```text
// UserService.java
public class UserService {
    private Fruit fruit = new Apple();
    public void getFruit() {
        fruit.get();
    }
}
```

然后，实现一个用户类，

```java
// User.java
public class User {
    public static void main(String[] args) {
        UserService user = new UserService();
        user.getFruit();
    }
}
```

上述就是我们实现一个程序的惯用方式，这样看上去没有什么问题，目前我们调用业务层`UserService`获取到**苹果**，那么试想一下，**如果现在我想获取橘子怎么办？**这样就需要修改业务层代码，

```java
// UserService.java
public class UserService {
    private Fruit fruit = new Orange();
    public void getFruit() {
        fruit.get();
    }
}
```

也许很多同学会认为这样没什么问题，那就修改一下业务层代码啊？

显然，这不是一个优秀的程序员做的事情，**每当用户需求做出改变时，我们的代码都要做出相应的修改**，那么有两个问题，

- 如果工程量较大，修改的内容较多怎么办？
- 如果我们修改代码对其他业务造成影响怎么办？

所以，一个好的设计思路就应该在不改变原代码的基础上实现我们想要的功能。

那么，接下来就应该转变思维，考虑一下，目前的**控制权**在业务层，所以每次用户需求改变时，业务层也要跟着改变，既然这样，我们把控制权交给用户不就行了吗？

下面来修改一下业务层的代码实现[控制权](https://www.zhihu.com/search?q=控制权&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1217542529})的转换，

```java
public class UserService {
    private Fruit fruit;
    public void setFruit(Fruit fruit) {
        this.fruit = fruit;
    }
    public void getFruit() {
        this.fruit.get();
    }
}
```

细心的同学应该可以看得出改变，我在加了一个**set**方法，使得用户层可以**注入**不同的对象，这样我们在用户层传入哪个对象，就会获得哪个结果，

```python
// 1. 获取橘子
public class User {
    public static void main(String[] args) {
        UserImpl user = new UserImpl();
        user.setFruit(new Orange());    //在这里注入对象
        user.getFruit();
    }
}
//2. 获取香蕉
public class User {
    public static void main(String[] args) {
        UserImpl user = new UserImpl();
        user.setFruit(new Banana());
        user.getFruit();
    }
}
```

现在来总结一下，经过改变前后到底发生了什么，

![img](https://pic3.zhimg.com/50/v2-523bbb35239175af0fb74a6583ffe55a_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-523bbb35239175af0fb74a6583ffe55a_720w.jpg?source=1940ef5c)

上图展示的很明确，就是控制权的反转，之前[主动权](https://www.zhihu.com/search?q=主动权&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1217542529})在业务层，每次用户提出需求业务层就需要跟着做出改变，现在我们把主动权交给了用户，它传进什么，就得到什么样的结果，这样业务代码就不用跟着改变了。

**这就是IOC（控制反转）的核心思想。**





#### 1.2实际讲解

之前的接口流程

1. UserDao接口
2. UserDaoImpl实现类
3. UserService业务接口
4. UserServiceImpl业务实现类

**之前的业务中，用户的需求可能会影响原来的代码，我们需要根据用户的需求修改代码；修改代码价格昂贵！**

因此使用`set`接口实现（程序不在具有主动性，变成了被动的接受，**程序员不用再去管理对象的创建，将将对象的创建转移给第三方，耦合性降低，可更加专注于业务实现，这就是IOC的原型（主动权由程序员--->用户）**）

```java
public class UserServiceImpl implements UserService{        //业务层要去调用DAO层
    //private UserDao userDao = new UserDaoImpl();   //1、若有多个dao则需要程序员切换不同的daoImpl，不合理

    private UserDao userDao;
    //2、利用set动态实现值的注入，想切换不同的值直接在spring中修改然后set进去即可，不用动代码
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void getUser() {
        userDao.getUser();
    }
}
```



**控制**:谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的,使用Spring后，对象是由Spring来创建的

**反转**:程序本身不创建对象,而变成被动的接收对象.

**依赖注入**:就是利用set方法来进行注入的.

**IOC是一种编程思想，由主动的编程变成被动的接收。**



#### IOC创建对象的方式

> 只要配置文件加载了，就说明在bean中创建了，容器内被管理的对象就已经被实例化了，无论调不调用都注册创建了（且对象都是一个）

1. 默认使用无参构造构建方法（若突然使用有参构造代替了会报错）

2. 若想使用有参构造构建方法，则需要

   1. 下标赋值

      ```xml
      <bean id="user" class="com.ldy.pojo.User">
          <constructor-arg index="0" value="有参构造构建方法" />
      </bean>
      ```

   2. 类型匹配（不建议使用）

      ```xml
      <!--有参构造   类型匹配法   基本类型可以直接用,引用类型必须要全写-->
      <bean id="user" class="com.ldy.pojo.User">
         <constructor-arg type="java.lang.String" value="有参构造构建方法--类型匹配" />
      </bean>
      ```

   3. 直接通过参数名构造

      ```xml
      <bean id="user" class="com.ldy.pojo.User">
          <constructor-arg name="name" value="有参构造构建方法" />
      </bean>
      ```

   具体代码如下：

   ```java
   package com.ldy.pojo;
   
   /**
    * @description:
    * @author: TianSu_Ldy
    * @createDate: 2021/6/1
    */
   public class User {
       private String name;
   
       public User() {
           System.out.println("User的无参构造");
       }
   
       //若想使用有参构造
       /*public User(String name) {
           this.name = name;
       }*/
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public void show(){
           System.out.println("name=" +name);
       }
   }
   ```

   ```xml
   <!--自己创建的资源文件beans.xml配置-->
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">
   
   
       <!--配置元数据,用于创建对象    bean就是java对象 , 由Spring创建和管理
       Java写法:  类型 变量名 = new 类型();
                  Hello hello = new Hello();
   
           bean =对象  new Hello();
           id就等于变量名   class等于new的对象
           property相当于给对象中的属性设置一个值
       -->
       <bean id="hello" class="com.ldy.dao.Hello">
           <property name="name" value="Spring"/>      <!--Spring是随机赋的值-->
       </bean>
   
       <!--默认使用无参构造来构建方法,换成有参会报错-->
       <bean id="user" class="com.ldy.pojo.User">
           <property name="name" value="ldy"/>
       </bean>
   
       <!--有参构造   下标赋值法-->
       <!--<bean id="user" class="com.ldy.pojo.User">
           <constructor-arg index="0" value="有参构造构建方法" />
       </bean>-->
   
       <!--有参构造   类型匹配法   基本类型可以直接用,引用类型必须要全写-->
       <!--<bean id="user" class="com.ldy.pojo.User">
           <constructor-arg type="java.lang.String" value="有参构造构建方法&#45;&#45;类型匹配" />
       </bean>-->
   
       <!--有参构造   -->
       <!--<bean id="user" class="com.ldy.pojo.User">
           <constructor-arg name="name" value="有参构造构建方法" />
       </bean>-->
   
   </beans>
   ```

   ```java
   //测试
   import com.ldy.dao.Hello;
   import com.ldy.pojo.User;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   /**
    * @description:
    * @author: TianSu_Ldy
    * @createDate: 2021/6/1
    */
   public class mytest {
       public static void main(String[] args) {
           //用xml加载元数据必须要这一行       获取spring的上下文对象
           ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
           //我们的对象现在都在Spring中的管理了，我们要使用，直接去里面取出来就可以!
           Hello hello = (Hello) context.getBean("hello");
           System.out.printf(hello.toString());
   
           User user = (User) context.getBean("user");
           user.show();
       }
   }
   ```



#### Spring中bean配置说明

###### 别名alias

```xml
<bean id="user" class="com.ldy.pojo.User">
        <property name="name" value="ldy"/>
    </bean>

<alias name="uesr" alias="随意起别名" />			之后获取user或者起的名都可以
```

###### Bean的配置

```xml
<!--
id:bean的唯一标识符,也就是我们学的对象名
class:bean对象所对应的全限定名:包名+类型
name:也是别名,比alias更高级,可以同时起多个别名,中间用空格,逗号,分号隔开
-->
<bean id="user" class="com.ldy.pojo.User" name="user1 user2,user3;user4">
        <property name="name" value="ldy"/>
    </bean>

<alias name="uesr" alias="随意起别名" />
```

###### import

一般用于团队开发,可以将多个配置文件导入为一个。（可能多个人都写了不同的xml配置，用import来合并）

```xml
<import resource="beans.xml" />
<import resource="beans1.xml" />
<import resource="beans2.xml" />	
在applicationContext.xml中可以导入beans.xml的配置，随后获取的时候只获取applicationContext.xml即可
```



#### 依赖注入DI

##### 构造器注入

即有参构造

```xml
	<!--有参构造   下标赋值法-->
    <!--<bean id="user" class="com.ldy.pojo.User">
        <constructor-arg index="0" value="有参构造构建方法" />
    </bean>-->

    <!--有参构造   类型匹配法   基本类型可以直接用,引用类型必须要全写-->
    <!--<bean id="user" class="com.ldy.pojo.User">
        <constructor-arg type="java.lang.String" value="有参构造构建方法&#45;&#45;类型匹配" />
    </bean>-->

    <!--有参构造   -->
    <!--<bean id="user" class="com.ldy.pojo.User">
        <constructor-arg name="name" value="有参构造构建方法" />
    </bean>-->
```

##### set方式注入

- 依赖注入：set方法注入！
  - 依赖：bean对象的创建依赖于Spring容器
  - 注入：bean对象中的所有属性，由容器来注入

【测试】

1. 复杂类型

   ```java
   public class Address {
       private String address;
   
       public String getAddress() {
           return address;
       }
   
       public void setAddress(String address) {
           this.address = address;
       }
       
   }
   ```

2. 真实测试对象

   ```java
   public class Student {
   
       private String name;            //常规类型
       private Address address;        //引用
       private String[] books;         //数组
       private List<String> hobbys;    //List
       private Map<String,String> card;    //Map
       private Set<String> games;      //Set
       private String wife;            //设置空指针
       private Properties info;        //Properties
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public Address getAddress() {
           return address;
       }
   
       public void setAddress(Address address) {
           this.address = address;
       }
   
       public String[] getBooks() {
           return books;
       }
   
       public void setBooks(String[] books) {
           this.books = books;
       }
   
       public List<String> getHobbys() {
           return hobbys;
       }
   
       public void setHobbys(List<String> hobbys) {
           this.hobbys = hobbys;
       }
   
       public Map<String, String> getCard() {
           return card;
       }
   
       public void setCard(Map<String, String> card) {
           this.card = card;
       }
   
       public Set<String> getGames() {
           return games;
       }
   
       public void setGames(Set<String> games) {
           this.games = games;
       }
   
       public String getWife() {
           return wife;
       }
   
       public void setWife(String wife) {
           this.wife = wife;
       }
   
       public Properties getInfo() {
           return info;
       }
   
       public void setInfo(Properties info) {
           this.info = info;
       }
   
       @Override
       public String toString() {
           return "Student{" +
                   "name='" + name + '\'' +
                   ", address=" + address +
                   ", books=" + Arrays.toString(books) +
                   ", hobbys=" + hobbys +
                   ", card=" + card +
                   ", games=" + games +
                   ", wife='" + wife + '\'' +
                   ", info=" + info +
                   '}';
       }
   }
   ```

3. applicationContext.xml

   ```xml
   <bean id="address" class="com.ldy.pojo.Address">
           <property name="address" value="武汉" />
       </bean>
   
       <bean id="student" class="com.ldy.pojo.Student">
           <!--普通值/常规类型注入-->
           <property name="name" value="刘德昱" />
           <!--引用类型注入-->
           <property name="address" ref="address" />
           <!--数组-->
           <property name="books">
               <array>
                   <value>红楼梦</value>
                   <value>三国演义</value>
               </array>
           </property>
           <!--List类型注入-->
           <property name="hobbys">
               <list>
                   <value>听歌</value>
                   <value>打篮球</value>
               </list>
           </property>
           <!--map类型注入-->
           <property name="card">
               <map>
                   <entry key="身份证" value="12121215121212" />
                   <entry key="银行卡" value="1212121512121215156" />
               </map>
           </property>
           <!--set类型注入-->
           <property name="games">
               <set>
                   <value>LOL</value>
                   <value>魔兽世界</value>
               </set>
           </property>
           <!--空指针类型注入-->
           <property name="wife">
               <null/>
           </property>
           <!--Properties类型注入-->
           <property name="info">
               <props>
                   <prop key="driver">1201920637</prop>
                   <prop key="url">xxxxxx.com</prop>
                   <prop key="username">sdsdfaf</prop>
                   <prop key="password">45454</prop>
               </props>
           </property>
       </bean>
   ```

4. 测试类

   ```java
   public class testcode {
   
       public static void main(String[] args) {
           //拿到上下文
           ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
           //拿到上下文中的student
           Student student = (Student) context.getBean("student");
           System.out.println(student.toString());
   
           /*打印输出
           Student{
           name='刘德昱',
           address=Address{address='武汉'},
           books=[红楼梦, 三国演义],
           hobbys=[听歌, 打篮球],
           card={
               身份证=12121215121212,
               银行卡=1212121512121215156},
           games=[LOL, 魔兽世界],
           wife='null',
           info={
               password=45454,
               driver=1201920637,
               url=xxxxxx.com,
               username=sdsdfaf}}
           */
       }
   }
   ```

   

##### 拓展方式注入

- p命名空间的xml快捷方式

> 对应的是set注入,用于"set注入"
>
> xmlns:p="http://www.springframework.org/schema/p"   添加此行依赖配置

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"	
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--可以直接使用   p:属性   来直接注入值-->
    <bean name="user" class="com.ldy.pojo.User" p:name="刘德昱" p:age=18 />


</beans>
```

- c命名空间的xml快捷方式

> 对应的是构造器注入,用于""有参构造"注入"
>
> xmlns:c="http://www.springframework.org/schema/c"  添加此行依赖配置

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user2" class="com.ldy.pojo.User" c:name="刘德昱2" c:age=188 />


</beans>
```



#### bean的作用域

> singleton 单例(全局只有1个对象)				prototype 原型(每一个bean创建一个其对应的对象)
>
> request				session
>
> application			websocket 

```xml
<bean id="user2" class="com.ldy.pojo.User" scope="singleton" />  默认是单例模式(所有的bean创建为一个对象)

<bean id="user2" class="com.ldy.pojo.User" scope="prototype" />		原型模式(每一个bean创建一个其对应的对象)


以下四种只能在web开发中使用


<bean id="user2" class="com.ldy.pojo.User" scope="request" />request：每一次 HTTP 请求都会产生一个新的实例，并且该 bean 仅在当前 HTTP 请求内有效。

<bean id="user2" class="com.ldy.pojo.User" scope="session" />Session - 每一次 HTTP 请求都会产生一个新的 bean，同时该 bean 仅在当前 HTTP session 内有效。

<bean id="user2" class="com.ldy.pojo.User" scope="application" />

<bean id="user2" class="com.ldy.pojo.User" scope="websocket" />
```

#### bean的自动装配

- 自动装配式满足bean依赖的一种方式
- Spring会在上下文中自动寻找,并自动给bean装配属性



在Spring中的三种装配方式

1. 在xml中显示的配置(如上)
2. 在Java中显示配置
3. 隐式的自动装配bean     ⭐
   1. 组件扫描(component scanning)：spring会自动发现应用上下文中所创建的bean；
   2. 自动装配(autowiring)：spring自动满足bean之间的依赖，也就是我们说的IOC/DI；

```java
public class Cat {
    public void shout() {
        System.out.println("miao~");
    }
}
```

```java
public class Dog {
    public void shout() {
        System.out.println("wang~");
    }
}
```

```java
public class People {
    @Autowired(required = false)    //@Autowired(required=false)  说明：false，对象可以为null；true，对象必须存对象，不能为null。
    private Dog dog;
    @Autowired(required = false)
    private Cat cat;
    private String name;

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    public Cat getCat() {
        return cat;
    }

    public void setCat(Cat cat) {
        this.cat = cat;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "People{" +
                "dog=" + dog +
                ", cat=" + cat +
                ", name='" + name + '\'' +
                '}';
    }
}
```

编写Spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
 
    <bean id="dog" class="com.kuang.pojo.Dog"/>
    <bean id="cat" class="com.kuang.pojo.Cat"/>
    <bean id="user" class="com.kuang.pojo.User">
        <property name="cat" ref="cat"/>
        <property name="dog" ref="dog"/>
        <property name="str" value="qinjiang"/>
    </bean>
</beans>
```

测试

```java
public class MyTest {
    @Test
    public void testMethodAutowire() {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        User user = (User) context.getBean("user");
        user.getCat().shout();
        user.getDog().shout();
    }
}
```

##### autowire byName (按名称自动装配)

```xml
<bean id="dog" class="com.kuang.pojo.Dog"/>
<bean id="cat" class="com.kuang.pojo.Cat"/>
<bean id="user" class="com.kuang.pojo.User" autowire="byName">
    <property name="str" value="qinjiang"/>
</bean>
```

当一个bean节点带有 autowire byName的属性时。

1. 将查找其类中(people类)所有的set方法名，例如setCat，获得将set去掉并且首字母小写的字符串，即cat。
2. 去spring容器中寻找是否有此字符串名称id的对象。
3. 如果有，就取出注入；如果没有，就报空指针异常。这里id="cat"若改为"catXXX"则找不到报空指针



##### autowire byType (按类型自动装配)

使用autowire byType首先需要保证：**<u>同一类型的对象，在spring容器中唯一</u>**。如果不唯一，会报不唯一的异常。

```xml
<bean id="dog" class="com.kuang.pojo.Dog"/>
<bean id="cat" class="com.kuang.pojo.Cat"/>
<bean id="user" class="com.kuang.pojo.User" autowire="byType">
    <property name="str" value="qinjiang"/>
</bean>
```

注:若加一行`<bean id="cat2" class="com.kuang.pojo.Cat"/>`则对象不唯一；**但是若将cat的bean名称改掉！测试！因为是按类型装配，所以并不会报异常，也不影响最后的结果。甚至将id属性去掉，也不影响结果**。



##### 注解

##### @Autowired

> @Autowired先byType，默认情况下必须要求依赖**对象**必须存在

1、在spring配置文件中引入context文件头

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"	1.在spring配置文件中引入context文件头
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context		1.在spring配置文件中引入context文件头
        http://www.springframework.org/schema/context/spring-context.xsd"	1.在spring配置文件中引入context文件头>
    
    <!--2.开启属性注解支持！-->
    <context:annotation-config/>

    <bean id="dog" class="com.lsy.dao.Dog"  />

    <bean id="cat" class="com.lsy.dao.Cat" />

    <bean id="people" class="com.lsy.dao.People">
 
</beans>
```

2、将User类中的set方法去掉，使用@Autowired注解

```java
public class People {
    //@Autowired(required = false)    //@Autowired(required=false)  说明：false，对象可以为null；true，对象必须存对象，不能为null。
    @Autowired
    private Dog dog;
    @Autowired
    private Cat cat;
    private String name;

    public Dog getDog() {
        return dog;
    }

//    public void setDog(Dog dog) {
//        this.dog = dog;
//    }

    public Cat getCat() {
        return cat;
    }

//    public void setCat(Cat cat) {
//        this.cat = cat;
//    }

    public String getName() {
        return name;
    }

    //public void setName(String name) {
    //    this.name = name;
    //}

    @Override
    public String toString() {
        return "People{" +
                "dog=" + dog +
                ", cat=" + cat +
                ", name='" + name + '\'' +
                '}';
    }
}
```

> 补充：
>
> @Autowired(required=false)  说明：false，对象可以为null；true，对象必须存对象，不能为null。
>
> @Qualifier
>
> - @Autowired是根据类型自动装配的，加上@Qualifier则可以根据byName的方式自动装配
> - @Qualifier不能单独使用。

##### 测试@Qualifier

```xml
<context:annotation-config/>
<!--配置文件修改内容，保证类型存在对象。且名字不为类的默认名字！-->
    <bean id="dog1" class="com.lsy.dao.Dog"  />
    <bean id="dog2" class="com.lsy.dao.Dog"  />
    <bean id="cat1" class="com.lsy.dao.Cat" />
    <bean id="cat2" class="com.lsy.dao.Cat" />

    <bean id="people" class="com.lsy.dao.People"/>
```



```java
	@Autowired
	//没有加Qualifier测试，直接报错
	//在属性上添加Qualifier注解
    @Qualifier(value = "dog2")
    private Dog dog;
    @Autowired
    @Qualifier(value = "cat2")
    private Cat cat;
    private String name;
```

> @Resource（可以单独使用，不用结合@Autowired使用）
>
> - @Resource如有指定的name属性，先按该属性进行byName方式查找装配；
> - 其次再进行默认的byName方式进行装配；
> - 如果以上都不成功，则按byType的方式自动装配。
> - 都不成功，则报异常。

##### 测试@Resource

```xml
<bean id="dog" class="com.kuang.pojo.Dog"/>
<bean id="cat1" class="com.kuang.pojo.Cat"/>
<bean id="cat2" class="com.kuang.pojo.Cat"/>
<bean id="user" class="com.kuang.pojo.User"/>
```

```java
public class User {
    //如果允许对象为null，设置required = false,默认为true
    @Resource(name = "cat2")
    private Cat cat;
    @Resource
    private Dog dog;
    private String str;
}
```

#### 现实注解开发

在Spring4之后,使用注解必须要导入aop的包。

##### bean的实现

##### 属性注入

```xml
<context:component-scan base-package="com.ldy.pojo" />

    <context:annotation-config/>
```



```java
//等价于<bean id="user">
@Component
public class User {
//    public String name = "ldy";

    //相当于<property name="name" value="lllllddddd">
    @Value("lllllldddd")
    public String name;


    //也可以注入在set方法上
    @Value("lllllldddd")
    public void setName(String name) {
        this.name = name;
    }
}
```

##### 衍生注解

> **@Compoent、@Repository、@Service、@Controller只是对应pojo、dao、service、controller各层，用法作用都是一样的。**是将某个类注册到Spring中，装配bean

@Compoent有几个衍生注解，我们在web开发中，会按照mvc三层架构分层!

- dao【@Repository】

  ```java
  @Repository
  public class UserDao {
  }
  ```

- service【@Service】

  ```java
  @Service
  public class UserService {
  }
  ```

- controller【@Controller】

  ```java
  @Controller
  public class UserController {
  }
  ```

  这时需要将bean中的配置改为`<context:component-scan base-package="com.ldy" />`

##### 自动装配注解

@Autowired：自动装配通过类型

​	如果Autowired不能唯一自动装配上属性，需要通过@Qualifier(value="xxx")

@Nullable：字段标记了这个注解，说明这个字段可以为null

@Resource：自动装配通过名字

##### 作用域

@Scope("singleton")等可以控制为单例、protoType

```java
@Component
@Scope("singleton")
public class User {
//    public String name = "ldy";

    //相当于<property name="name" value="lllllddddd">
    //@Value("lllllldddd")
    public String name;


    //也可以注入在set方法上
    @Value("lllllldddd")
    public void setName(String name) {
        this.name = name;
    }
}
```

##### 小结

xml与注解

- xml更万能，适用于任何场合，维护上简单方便
- 注解不是自己的类使用不了，维护相对复杂

xml与注解最佳实践：

- xml用来管理bean

- 注解 只负责完成属性的注入（即value值的注入）

- 使用过程中组要注意：必须要让注解生效，开启注解的支持

  ```java
  <!--指定要扫描的包，这个包下的注解就会生效-->
  <context:component-scan base-package="com.ldy" />
  <context:annotation-config/>
  ```



#### 使用Java的方式配置Spring

现在完全不使用xml配置了，全交给Java来做！

```Java
//说明这个类被Spring接管，注册到容器中
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("刘德昱")   //属性注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

配置类

```Java
//配置文件
//@Configuration代表这是一个配置类，就和之前看的beans.xml是一样的
@Configuration      //其也会被Spring容器托管，注册到容器中，因为其本身也是一个@Component
@ComponentScan("com.ldy.pojo")	//可以加很多这样的配置
@Import(config2.class)		//引入不同的配置来实现整体配置加载
public class ldyConfig {
    //等价于xml中写的一个bean标签；
    //方法的getUser名字就相当于bean标签中的id属性
    //返回值相当于bean标签中的class属性
    @Bean
    public User getUser() {
        return new User();//返回要注入到的bean对象
    }

}
```

测试类

```Java
public class MyTest {
    public static void main(String[] args) {
       	//如果完全使用了配置类方式去做，就只能使用 AnnotationConfigApplicationContext上下文获取容器，通过配置类的class对象加载
        ApplicationContext context = new AnnotationConfigApplicationContext(ldyConfig.class);
        User getUser = (User) context.getBean("getUser");//取方法名
        System.out.println(getUser.getName());
    }
}
```

**这种纯Java的配置方式，在SpringBoot中随处可见！**



### 2、AOP面向切面编程

#### 代理模式

是Spring AOP的底层；

- 静态代理
- 动态代理

![](https://github.com/Daryliu/Typora-Essay/blob/master/image/%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F.png)

##### 静态代理

角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决 （如上图的“租房”）

  ```Java
  //租房
  public interface Rent {
      void rent();
  }
  ```

- 真实角色：被代理的角色（房东）

  ```Java
  //房东
  public class Host implements Rent{
  
      @Override
      public void rent() {
          System.out.println("房东要出租房子");//房东是只租房子。其他多余的事情都不做，都是中介做
      }
  }
  
  ```

- 代理角色：代理真实角色，代理后会做一些附属操作！（中介）

  ```java
  //中介代理
  public class rentProxy implements Rent{
  
      private Host host;
  
      public rentProxy(){
  
      }
  
      public rentProxy(Host host) {
          this.host = host;
      }
  
      @Override
      public void rent() {
          seeHouse();
          host.rent();    //帮房东租房子
          hetong();
          fare();
      }
  
  
      //看房
      public void seeHouse() {
          System.out.println("中介带你看房");
      }
  
      //收中介费
      public void fare() {
          System.out.println("收中介费");
      }
  
      //签合同
      public void hetong() {
          System.out.println("签租赁合同");
      }
  
  }
  ```

- 客户：访问代理对象的人（租房者）

  ```java
  //客户
  public class Client {
      public static void main(String[] args) {
  
          Host host = new Host();//之前是直接找房东租房子
          //代理---客户找代理租房子，代理需要拿到房东的房子出租权（而且中介会有附加的操作）
          rentProxy proxy = new rentProxy(host);
          proxy.rent();
  
      }
  }
  ```

  

代理模式的优缺点

- 可以使真实角色的操作更加纯粹；
- 公共业务就交给代理角色，实现了 业务的分工
- 公共业务发生扩展的时候，方便集中管理
- 一个真实角色就会产生一个代理角色，代码量会翻倍，开发效率会降低
- 一个动态代理类可以代理多个类，只要是实现同一个接口即可。



#### AOP

AOP：面向切面编程。面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一 种技术。AOP是0OP的延续， 是软件开发中的一一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发效率。

##### AOP在Spring中的作用

**提供声明式事务。允许用户自定义界面。**

● 横切关注点:<u>跨越应用程序多个模块的方法或功能</u>。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志,安全,缓存,事务等等...
● 切面(ASPECT) :横切关注点被模块化的特殊对象。即，它是一个类。
● 通知(Advice) :切面必须要完成的工作。即,它是类中的一一个方法。
● 目标(Target) :被通知对象。
● 代理(Proxy) :向目标对象应用通知之后创建的对象。
● 切入点(PointCut) :切面通知执行的"地点”的定义;
● 连接点(JointPoint) :与切入点匹配的执行点。

##### 在Spring中使用aop

1. 需要先提前导入依赖

   ```xml
   		<dependency>
               <groupId>org.aspectj</groupId>
               <artifactId>aspectjweaver</artifactId>
               <version>1.9.7</version>
               <scope>runtime</scope>
           </dependency>
   ```

2. 

   1. 方法一：使用Spring的api接口https://github.com/Daryliu/SpringMaven/tree/master/JDproxy/src/main/java/com/ldy
   2. 方法二：使用自定义类实现接口



#### 报错解决

1. `Caused by: java.lang.IllegalArgumentException: Unsupported class file major version 59`

```java
解决方法：
jdk13暂时不被支持,降级至jdk11,可正常运行

降级方法(IDEA)
File->Settings->Build,Execution,Deployment->Compiler->Java Compiler->project bytecode version 选择11

File->Project Structure->Project->

Porject Sdk 选择11

Porject language level 选择11

File->Project Structure->Modules->language level 选择11
```

### 3、mybatis-spring

在 MyBatis-Spring 中，可使用SqlSessionFactoryBean来创建 SqlSessionFactory。要配置这个工厂 bean，只需要把下面代码放在 Spring 的 XML 配置文件中：

```sql
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <property name="dataSource" ref="dataSource" />
</bean>
```

### 4、

#### Spring @RestController、@Controller区别

**用@Controller，返回的是页面；@Controller加上@ResponseBody，返回的是JSON、XML或其他文本。**

```kotlin
@Controller
@RequestMapping("/test")
public class MyController1 {
    
    @ResponseBody
    @GetMapping(path="/get1", produces = "text/plain;charset=utf-8")
    public String getMethod1(String str) {
        return str;
    }

    @GetMapping(path="/get2", produces = "text/plain;charset=utf-8")
    public String getMethod2(String str) {
        return str;
    }
}
```

**访问 /test/get1，并携带参数 str="index" ，返回 index 字符串。
 访问 /test/get2，并携带参数 str="index" ，返回名为 index 页面，如index.jsp。**

#### java 中component_Spring中@Component和@Bean的区别

Spring管理Bean分为两个部分，一个是注册Bean，一个装配Bean。

完成这两个动作有三种方式，一种是使用自动配置的方式、一种是使用JavaConfig的方式，一种就是使用XML配置的方式。

**@Component 把普通pojo实例化到spring容器中**

**@Bean 需要在配置类中使用，即类上需要加上@Configuration注解**

两者都能通过@Autowired注解自动装配

在应用开发的过程中，如果想要将**<u>第三方库</u>**中的组件装配到你的应用中，在这种情况下，是<u>没有办法在它的类上添加@Component和@Autowired注解</u>的，因此就不能使用自动化装配的方案了。

但是可以通过xml 或者在@Configuration配置类中通过@Bean进行配置
