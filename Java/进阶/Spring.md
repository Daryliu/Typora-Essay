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



#### Spring配置说明
