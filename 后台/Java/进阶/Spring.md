# Spring

---

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



#### IOC控制反转

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
    //2、利用set动态实现值的注入
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

**IOC是一种编程思想，由主动的编程变成被动的接收.**



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
   2. 自动装配(autowiring)：spring自动满足bean之间的依赖，也就是我们说的IoC/DI；

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
        http://www.springframework.org/schema/context	1.在spring配置文件中引入context文件头
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

> @Resource
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
    //@Value("lllllldddd")
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

