[TOC]

### MyBatis

---

MyBatis 是一款优秀的**持久层框架**

- 支持自定义 SQL、存储过程以及高级映射。
-  免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

---

#### 1、持久层

##### 1.1	持久化

数据持久化

- 持久化就是将程序的数据在<u>持久状态</u>和<u>瞬时状态</u>转化的过程。
- 内存（断电即失）
- 持久化操作：数据库（jdbc）、io文件持久化

**为什么要持久化？**

- 有一些对象，不能让它丢掉（外）
- 内存太贵了（内）



##### 1.2	持久层

- 完成持久化工作的代码块
- 层界限十分明显



##### 1.3	为什么要Mybtis

- 传统的JDBC太麻烦了。为了简化、是框架、自动化
- 帮助程序员将数据存入数据库中

---

#### 2、搭建环境

1. 创建数据库

   - ```sql
     CREATE DATABASE 'mybatis';
     use 'mybatis';
     
     CREATE TABLE 'user' (
         'id' INT(20) NOT NULL PRIMARY KEY,
         'name' VARCHAR(50) DEFAULT NULL,
         'pwd' VARCHAR(50) DEFAULT NULL
     )ENGINE=INNODB DEFAULT CHARSET=utf8;
     
     INSERT INTO 'user'('id','name','pwd') VALUES (1,'张三','123'),(2,'张1','123')
     ```

2. 新建项目

   - 创建Maven项目
   - pom.xml配置，导入jar包依赖

   ```xml
   		//mybatis配置
   		<dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.5.6</version>
           </dependency>
   		//单元测试配置
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.11</version>
           </dependency>
   		//mysql配置
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.19</version>
           </dependency>
   ```

   - 在资源文件中创建mabatis-config.xml文件，编写mybatis核心配置。

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
                   "http://mybatis.org/dtd/mybatis-3-config.dtd">
           <!--核心配置文件-->
   <configuration>
   <!--配置环境-->
   <environments default="development">
       <environment id="development">
           <transactionManager type="JDBC"/><!--事务管理-->
           <dataSource type="POOLED">
               <property name="driver" value="com.mysql.jdbc.Driver"/>
               <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
               <property name="username" value="root"/>
               <property name="password" value="123456"/>
           </dataSource>
       </environment>
   </environments>
       <!--注册mapper-->
       <mappers>
           <mapper resource="com/daryl/dao/UserMapper.xml"/>
       </mappers>
   </configuration>
   ```

   - 编写一个工具类，从xml中构建SqlSessionFactory**（他的能力就是打开一个SqlSession会话，而且重载了许多不同的参数，你可以改变这些参数自定义会话过程中的一些默认行为）**；（sqlSessionFactory用于创建sqlSession，而sqlSession**（是sql中的会话）**相当于JDBC中的statement，执行sql操作用）**SqlSession表示的是数据库客户端和数据库服务端之间的一种会话，并维护了两者之间的状态信息；SqlSession是一个接口，里面有我们熟悉的操作数据库执行sql语句的select、insert、update等方法**

   ```Java
   //sqlSessionFactory用于创建sqLSession，而sqLSession相当于JDBC中的statement，执行sql操作用（是sql中的会话）
   private static SqlSessionFactory SqlSessionFactory;
   
       static {//设置为静态代码块，进来就执行
   
           try {
               //获取sqlSessionFactory对象
               String resource = "mybatis-config.xml";
               InputStream inputStream = Resources.getResourceAsStream(resource);
               SqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
           } catch (IOException e) {
               e.printStackTrace();
           }
   
       }
   
       //既然有了 SqlSessionFactory ，顾名思义，我们就可以从中获得 SqlSession 的实例了。
       // SqlSession 完全包含了面向数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句
       public static SqlSession getSqlSession() {
           return SqlSessionFactory.openSession();//SqlSession用于操作数据库
       }
   ```



#### 3、编写代码

- 写实体类

```Java
//实体类
public class User {
    private int id;
    private String username;
    private String pwd;

    public User() {
    }

    public User(int id, String username, String pwd) {
        this.id = id;
        this.username = username;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```

- Dao接口

```Java
public interface UserDao {
    List<User> getUserList();
}
```

- 接口实现类（由原来的UserDaoImpl转换为一个Mapper.xml配置文件）**连接了Dao接口和实体类**

```xml
<mapper namespace="com.daryl.dao.UserDao">     <!--命名空间，绑定一个对应的Dao接口（Mapper）-->
    <!--查询语句-->
    <select id="getUserList" resultType="com.daryl.entity.User">    /*相当于选择UserDao接口中的getUserList方法，返回User类型的结果*/
		select * from mybatis.user
    </select>
</mapper>
```

---



#### 4、junit测试

```java
public class UserDaoTest {
    @Test
    public void test() {
        //1、获得SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //2、执行SQL  得到UserDao接口  执行里面的方法
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> userList = mapper.getUserList();

        for (User user : userList) {
            System.out.println(user);
        }
        sqlSession.close();
    }
}
```

可能遇到的问题？

1. 配置文件没有注册
2. 绑定接口错误
3. 方法名不对
4. 返回类型不对
5. Maven导出资源问题

---

#### 5、增删改查CRUD实现

1. namespace

   - namespace内部的包名要与Dao/Mapper接口中的包名一致

2. select

   - id：对应的namespace中的方法名
   - resultType：Sql语句执行的返回值类型 
   - parameterType：参数类型

3. Insert、update、delete

   1. 编写接口

      ```Java
      public interface UserMapper {
          //查询全部用户
          List<User> getUserList();
          //根据id查询
          User getUserById(int id);
          //增加一个用户
          int addUser(User user);
          //修改用户
          int updateUser(User user);
          //删除一个用户
          int deleteUser(int id);
      }
      ```

   2. 编写mapper中的语句

      ```Java
      <!--下面一句 相当于选择UserDao接口中的getUserList方法，返回User类型的结果-->
          <select id="getUserList" resultType="com.daryl.entity.User">
              select * from mybatis.user
          </select>
          
          <select id="getUserById" parameterType="int" resultType="com.daryl.entity.User">
              select * form mybatis.user where id = #{id};
          </select>
      
          <!--对象中的属性可以直接取出来-->
          <insert id="addUser" parameterType="com.daryl.entity.User">
              insert into mybatis.user (id, name, pwd) VALUES (#{id},#{name},#{pwd});
          </insert>
      
          <update id="updateUser" parameterType="com.daryl.entity.User">
              update mybatis.user
              set name=#{name},pwd=#{pwd}
              where id=#{id};
          </update>
      
          <delete id="deleteUser" parameterType="int">
              delete from mybatis.user where id=#{id};
          </delete>
      ```

   3. 测试

      ```Java
      @Test
          public void getUserById() {
              SqlSession sqlSession = MybatisUtils.getSqlSession();
      
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              User user = mapper.getUserById(1);
              System.out.println(user);
      
              sqlSession.close();
          }
      
          //增删改需要提交事务
          @Test
          public void addUser() {
              SqlSession sqlSession = MybatisUtils.getSqlSession();
      
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              int res = mapper.addUser(new User(4, "刘德昱", "3556"));
              if (res > 0) {
                  System.out.println("插入成功");
              }
              //提交事务
              sqlSession.commit();
      
              sqlSession.close();
          }
      
          @Test
          public void updateUser() {
              SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              mapper.updateUser(new User(2,"张2","123123"));
      
              //提交事务
              sqlSession.commit();
              sqlSession.close();
          }
      
          @Test
          public void deleteUser(){
              SqlSession sqlSession = MybatisUtils.getSqlSession();
              UserMapper mapper = sqlSession.getMapper(UserMapper.class);
              mapper.deleteUser(1);
      
              //提交事务
              sqlSession.commit();
      
              sqlSession.close();
          }
      ```
   
   ###### 4、模糊查询
   
   1. Java代码执行的时候，传递通配符% %；
   2. 在sql拼接中使用通配符！
   
   ```java
   Dao中：
   List<User> getUserLike(String name);
   
   Dao对应的Mapper：
   	<select id="getUserLike" resultType="com.daryl.entity.User">
       	select * from mybatis.user where name like #{name}
       </select> 
   测试代码：
            @Test
       public void getUserLike(){
           SqlSession sqlSession = MybatisUtils.getSqlSession();
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           List<User> userList = mapper.getUserLike("%王%");//%王%：意思是模糊查询带”王”的数据
           //%王：意思是查找出以“王”结尾的数据
           
           for(User user:userList){
               System.out.println(user);
           }
   
           sqlSession.close();
       }
   ```



#### 6、万能的Map

若我们的实体类或者数据库表中**字段或参数过多时**，我们应该考虑用Map或者用<u>注解</u>。（不够正规）

```Java
int addUser(Map<String,Object> map);
------------------------
<insert id="addUser2" parameterType="map">		//其中map是Map的别名
        insert into mybatis.user (id, name, pwd) VALUES (#{userId},#{userName},#{userPwd});//map的话后面可以随便起名
    </insert>
---------------------------
     @Test
    public void addUser() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String,Object> map = new HashMap(String,Object);
        map.put("userId",5);
        map.put("userName","王五");
        map.put("userPwd","123123");

        sqlSession.close();
    }   
```

Map传递参数，直接在sql中取出key即可；

对象传递参数，直接在sql中去对象的属性即可；

只有一个参数的情况下，可直接在sql中取到。

---



#### 7、配置解析

编写顺序：

<img src="C:\Users\dn3\AppData\Roaming\Typora\typora-user-images\image-20201102213057507.png" alt="image-20201102213057507" style="zoom:150%;" />

##### 7.1核心配置文件

- mybatis-config.xml
- Mybatis的配置文件包含了会深深影响Mybatis行为的设置和属性信息。

```xml
properties 属性
settings 设置
typeAliases 类型命名
typeHandlers 类型处理器
objectFactory 对象工厂
plugins 插件
environments 环境
environment 环境变量
transactionManager 事务管理器
dataSource 数据源
databaseIdProvider 数据库厂商标识
mappers 映射器
```

###### 7.2 环境配置

MyBatis 可以配置成适应多种环境，**不过要记住：尽管可以配置多个环境，每个 SqlSessionFactory 实例只能选择其一**

默认事务管理器JDBC,默认数据源POOLED

```xml
<environments default="development">			默认环境是development
    <environment id="development">
        <transactionManager type="JDBC"/>			事务管理器JDBC/MANAGED
        <dataSource type="POOLED">				数据源UNPOOLED/POOLED/JNDI（连接数据库的作用）默认是连接池，用完回收		dbcp/c3p0
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
            <property name="username" value="root"/>
            <property name="password" value="123456"/>
        </dataSource>
    </environment>
</environments>
```

###### 7.3 属性(properties)

可以通过**properties属性来实现引用配置文件**。

这些属性都是可外部配置且可动态替换的，既可以在典型的 Java 属性文件中配置，亦可通过 properties 元素的子元素来传递。【例如：db.properties】

例：

- 编写一个db.properties

```xml
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8
username=root
password=123456
```

- **在核心配置文件mybatis-config.xml中引入，则在核心配置文件中数据源dataSource中的也可以引用，不用直接写了！**

<u>也可以在db.properties写一半，在properties属性中写一半（优先使用外部引用的）</u>

```xml
<properties resource="db.properties"/>


<environments default="development">			
    <environment id="development">
        <transactionManager type="JDBC"/>			
        <dataSource type="POOLED">				
            <property name="driver" value="${driver}"/> 修改一下四步即可，直接引用；也可以在db.properties写一半，在properties属性中写一半（优先使用外部引用的）
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```

###### 7.4 类型别名typeAliases

- 类型别名是为 Java 类型设置一个短的名字。
- 它只和 XML 配置有关，存在的意义仅在于用来减少类完全限定名的冗余。（**实体类少的时候使用**，可diy）

```xml
<typeAliases>
    <typeAlias type="com.daryl.pojo.User" alias="User">			将com.daryl.pojo.User起别名为User
</typeAliases>
```

- 也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean【**扫描实体类的包，他的默认别名就是这个类的类名的首字母小写**】<u>实体类多的时候使用</u>

```xml
<typeAliases>
    <package name="com.daryl.pojo"/>			扫描com.daryl.pojo包找到实体类，并起别名为其小写user
</typeAliases>
```

- 也可以使用注解【在实体类中加注解，Mapper中即可直接引用】**<font color="greenblue">注解必须使用扫描包的形式</font>**

```Java
@Alias("User")   		注解为User，其他地方可以引用
//实体类

public class User {
    private int id;
    private String username;
    private String pwd;

    public User() {
    }

    public User(int id, String username, String pwd) {
        this.id = id;
        this.username = username;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```

###### 7.5 设置settings

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。

| 设置参数                                                   | 描述                                                         | 有效值                                                       | 默认值  |
| ---------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------- |
| mapUnderscoreToCamelCase（将数据库列名改为Java的类似映射） | 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。 | true \| false                                                | False   |
| logImpl（打印日志）                                        | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | Not set |

###### 7.6 映射器Mappers

首先我们需要告诉 MyBatis 到哪里去找到这些语句。

1. 方式一：<u>使用相对于类路径的资源引用</u>

   - ```xml
     <mappers>
       <mapper resource="com/daryl/dao/UserMapper.xml"/>
       <mapper resource="com/daryl/dao/BlogMapper.xml"/>
       <mapper resource="com/daryl/dao/PostMapper.xml"/>
     </mappers>
     ```

   方式二：使用映射器接口实现类的完全限定类名                【通过找Mapper对应的类来找到mapper】

   - ```xml
     <mappers>
       <mapper class="com.daryl.dao.UserMapper"/>
       <mapper class="com.daryl.dao.BlogMapper"/>
       <mapper class="com.daryl.dao.PostMapper"/>
     </mappers>
     ```

     <font color="greenblue">问题：使用calss注册mapper时，可能找不到对应的Mapper的xml！</font>

     要求：

     ​		**接口和他的Mapper配置文件必须同名！**

     ​		**接口和他的Mapper配置文件必须在同一文件下！**

2. 方式三：将包内的映射器接口实现全部注册为映射器

   - ```xml
     <mappers>
       <package name="com.daryl.dao"/>
     </mappers>
     ```

     <font color="greenblue">问题：使用包注册mapper时，可能找不到对应的Mapper的xml！</font>

     要求：

     ​		**接口和他的Mapper配置文件必须同名！**

     ​		**接口和他的Mapper配置文件必须在同一文件下！**

###### 7.7 生命周期和作用域

**错误的时候会出现并发问题。**

**流程**：程序开始时创建SqlSessionFactoryBuilder（创建完就不需要，因此在**<u>局部变量</u>**）----->创建SqlSessionFactory工厂----->生产SqlSession----->读取Mapper.xml----->结束

**SqlSessionFactoryBuilder**

- 创建完就不需要，
- 放在**<u>局部变量</u>**

**SqlSessionFactory**

- 可看作数据库连接池
- 创建就一直存在，没有理由丢弃或者重新创建
- 最佳作用域：应用作用域
- 最简单的使用是**单例模式/静态单例模式**

**SqlSession**

- 连接到连接池的一个请求
- 需要关闭
- 最佳作用域：方法中（用完后关闭，否则会被占用）

###### 7.8 解决属性名和字段名不一致的问题

问题：

​		若数据库中User表字段为id,name，password；而我们写的实体类User中字段为id，name，pwd；则二者不对应？

解决方法：

- 起别名			password as pwd

  - ```java
    select id,name,password as pwd from mybatis.user where id = #{id}
    ```

- 改Mapper.xml中的ResultMap（结果集映射）

  - ```xml
    id name password	数据库
    id name pwd		实体类				不一致
    
    
    //改Mapper.xml中的ResultMap（结果集映射）
    <resultMap id="UserMap" type="User">
    	<!--column对应数据库中的字段；property对应实体类中的属性-->
    	<result column="id" property="id">
        <result column="name" property="name">
        <result column="password" property="pwd">
    </resultMap>
    
    <select id="getUserById" resultMap="UserMap">		//原来是在resultType中写为con.daryl.pojo.User实体类，现在是映射为User
    	select * from mybatis.user where id =#{id}
    </select>
    ```


###### 7.9	日志工厂

若数据库操作出现了异常，需要拍错，则日志就是最好的助手！

| 设置名  | 描述                                                  | 有效值                                                       | 默认值 |
| ------- | ----------------------------------------------------- | ------------------------------------------------------------ | ------ |
| logImpl | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。 | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置 |

在setting中设置：

```xml
<settings>
    <setting name="logImpl" value="有效值"/>	//STDOUT_LOGGING是标准的日志工厂实现
</settings>
```

- STDOUT_LOGGING是标准的日志工厂实现

- LOG4J：

  - Log4j是[Apache](https://baike.baidu.com/item/Apache/8512995)的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件；

  - 1、需要导入包

    - ```xml
      <!-- https://mvnrepository.com/artifact/log4j/log4j -->
      <dependency>
          <groupId>log4j</groupId>
          <artifactId>log4j</artifactId>
          <version>1.2.17</version>
      </dependency>
      ```

  - 2、在类路径下创建log4j.properties文件进行配置

    - ```properties
      #将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
      log4j.rootLogger=DEBUG,console,file
      
      #控制台输出的相关设置
      log4j.appender.console = org.apache.log4j.ConsoleAppender
      log4j.appender.console.Target = System.out
      log4j.appender.console.Threshold=DEBUG
      log4j.appender.console.layout = org.apache.log4j.PatternLayout
      log4j.appender.console.layout.ConversionPattern=【%c】-%m%n		//格式
      
      #文件输出的相关设置
      log4j.appender.file = org.apache.log4j.RollingFileAppender
      log4j.appender.file.File=./log/daryl.log		//输出文件的位置
      log4j.appender.file.MaxFileSize=10mb
      log4j.appender.file.Threshold=DEBUG
      log4j.appender.file.layout=org.apache.log4j.PatternLayout
      log4j.appender.file.layout.ConversionPattern=【%p】【%d{yy-MM-dd}】【%c】%m%n
      
      #日志输出级别
      log4j.logger.org.mybatis=DEBUG
      log4j.logger.java.sql=DEBUG
      log4j.logger.java.sql.Statement=DEBUG
      log4j.logger.java.sql.ResultSet=DEBUG
      log4j.logger.java.sql.PreparedStatement=DEBUG
      ```
      
    
  - 3、配置log4j为日志的实现（mybatis-config.xml中）
  
    - ```xml
      <settings>
          <setting name="logImpl" value="" />
      </settings>
      ```
  
  - 4、Log4j的使用，直接测试运行上面的查询
  
    - ```Java
      //1、在要使用Log4j的类中导入包import org.apache.log4j.Logger;
      //2、日志对象，参数为当前类的class
      static Logger logger = Logger.getLogger(UserDaoTest.class);
      //3、使用
      @Test
      public void testLogger(){
          Logger.info("info");		//3中日志级别
          logger.degug("debug");
          logger.error("error");
      }
      ```

#### 8、 Limit实现分页

```sql
SELECT * FROM user limit 0,2;		//查询从第0个开始，每页2个
SELECT * FROM user limit 2;			//查询从0开始，到2
SELECT * FROM user limit 4,-1;		//查询从第4个开始，直到最后一个（已经被修复了，不再使用）
```

使用mybatis实现分页，核心是Sql！

1. 在UserMapper.class接口 中

   - ```Java
     //实现分页
     List<User> getUserByLimit(Map<String,Integer> map);
     ```

2. 在UserMapper.xml中（sql实现分页）

   - ```xml
     <!--分页查询，Map的别名就是map-->
     <select id="getUserByLimit" parameterType="map" resultType="com.daryl.entity.User">
         select * from mybatis.user limit #{startIndex},#{pageSize}
     </select>
     ```

3. 在测试代码中

   - ```java 
     @Test
     public void getUserLimit(){
         SqlSession sqlSession = MybatisUtils.getSqlSession();
         UserMapper mapper = sqlSession.getMapper(UserMapper.class);
         HashMap<String, Integer> map = new HashMap<String, Integer>();
         map.put("startIndex",0);
         map.put("pageSize",2);
         List<User> userLimit = mapper.getUserByLimit(map);
         for (User user : userLimit) {
             System.out.println(user);
         }
         sqlSession.close();
     }
     ```

#### 9、 RowBounds类 分页（面向对象；不建议使用）

1. 在UserMapper.class接口 中

   - ```Java
     //实现分页
     List<User> getUserByRowBounds();
     ```

2. 在UserMapper.xml中

   - ```xml
     <!--分页查询，Map的别名就是map-->
     <select id="getUserByRowBounds" resultType="com.daryl.entity.User">
         select * from mybatis.user 
     </select>
     ```

3. 在测试代码中（分页交给面向对象的Java语句做）

   - ```java 
     @Test
     public void getUserByRowBounds(){
         SqlSession sqlSession = MybatisUtils.getSqlSession();
         //RowBounds实现分页
         RowBounds rowBounds = new RowBounds(0,2);//从第0个开始每页两个
         //通过Java代码层面实现分页
         List<User> userlist = sqlSession.selectList("com.daryl.dao.UserMapper.getUserByRowBounds",null,rowBounds);	
         for (User user : userLimit) {
             System.out.println(user);
         }
         sqlSession.close();
     }
     ```

4. **分页插件**

   - **<u>MyBatis的PageHelper分页插件（方法与RowBounds一样）</u>**

---

#### 10、 使用注解开发

对于简单语句来说，注解使代码显得更加简洁，然而 Java 注解对于稍微复杂的语句就会力不从心并且会显得更加混乱。因此，如果你需要做很复杂的事情，那么最好使用 XML 来映射语句。

- **注解**：不再需要UserMapper.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

- 替换为：

```Java
package org.mybatis.example;
public interface BlogMapper {
  @Select("SELECT * FROM user WHERE id = #{id}")
  User selectBlog(int id);
}
```

- 在mybatis-config.xml核心配置文件中绑定接口的类：

```xml
<!--绑定接口-->
<mappers>
    <mapper class="com.daryl.dao.UserMapper" />
</mappers>
```

- 测试代码：

```Java
@Test
public void selectBlog(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    //底层主要用反射机制
    UserMapper mapper =sqlSession.getMapper(UserMapper.class);	
    List<User> userlist = mapper.getUsers();
    for (User user : userLimit) {
        System.out.println(user);
    }
    sqlSession.close();
}
```

##### 10.1面向接口编程

接口：解耦！！上层不管具体的实现。接口实现了定义与实现的分离。

##### 10.2注解实现CRUD

1. 工具类修改为自动提交事务（true）

   ```Java
   private static SqlSessionFactory SqlSessionFactory;
   static {
       try {
           String resource = "mybatis-config.xml";
           InputStream inputStream = Resources.getResourceAsStream(resource);
           SqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
       } catch (IOException e) {
           e.printStackTrace();
       }
   
   }
   public static SqlSession getSqlSession() {
       return SqlSessionFactory.openSession(true);//自动提交事务
   }
   ```

2. UserMapper.java接口文件

   - ```Java
     //通过id查询
     public interface UserMapper {
         @Select("select * from user")
         List<User> getUsers();
         
         @Select("select * from user where id=#{id}")
         List<User> getUserById(@Param("id") int id);//存在多个参数必须加@Param。sql语句取值也是从@Param中取的
         
         @Insert("insert into user(id,name,pwd) values (#{id},#{name},#{password})")	//后面是实体类中的变量名
         int addUser(User user);
         
         @Update("update user set name=#{name},pwd=#{password},id=#{id})
         int updateUser(User user);
         
         @Delete("delete from user where id=#{id}")
         int deleteUser(@Param("id") int id);
     }
     ```

3. 在mybatis-config.xml核心配置文件中绑定接口的类：

   - ```xml
     <!--绑定接口-->
     <mappers>
         <mapper class="com.daryl.dao.UserMapper" />
     </mappers>
     ```

4. 测试

   - ```java
     @Test
     public void getUserById(){//查询
         SqlSession sqlSession = MybatisUtils.getSqlSession();
         UserMapper mapper =sqlSession.getMapper(UserMapper.class);	
         User user = mapper.getUserById(1);
         System.out.println(user);
         sqlSession.close();
     }
     
     @Test
     public void addUser(){//增加
         SqlSession sqlSession = MybatisUtils.getSqlSession();
         UserMapper mapper =sqlSession.getMapper(UserMapper.class);	
         mapper.addUser(new User(5,"刘","1111"));
         sqlSession.close();
     }
     
     
     @Test
     public void updateUser(){//增加
         SqlSession sqlSession = MybatisUtils.getSqlSession();
         UserMapper mapper =sqlSession.getMapper(UserMapper.class);	
         mapper.updateUser(new User(5,"德语","23232"));
         sqlSession.close();
     }
     
     @Test
     public void deleteUser(){//删除
         SqlSession sqlSession = MybatisUtils.getSqlSession();
         UserMapper mapper =sqlSession.getMapper(UserMapper.class);	
         mapper.deleteUser(1);
         sqlSession.close();
     }
     ```

**关于@Param()注解**

- 基本类型的参数或者String类型的参数，需要加上
- 引用类型不用加
- 若只有一个基本类型的话可以不加，但是建议加上
- 在sql中引用的就是@Param()设定的属性名！



**#{}和${}的区别？**

- 第一种可以防止sql注入
- 用第二种不安全



#### 11、 Lombok插件的使用

Project Lombok是一个java库，它可以自动插入到编辑器和构建工具中，只用在类上加注解，为java增添趣味。

使用步骤：

1. 在idea安装lombok插件

2. 在项目中导入lombok的jar包

   - ```xml
     <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
     <dependency>
         <groupId>org.projectlombok</groupId>
         <artifactId>lombok</artifactId>
         <version>1.18.16</version>
         <scope>provided</scope>
     </dependency>
     ```

3. 在程序**“实体类”**上加注解

```Java
@Getter and @Setter
@FiledNameConstants
@ToString（生成toString）
@EqualsAndHashCode
@AllArgsConstructor（有参构造）,@RequiredArgsConstructor and @NoArgsConstructor（无参构造）
@Log,@Log4j,@Log4j2,@slf4j,@CommonsLog,@JBossLog,@Flogger
@Data		//在类上加了这个之后，就不用写get、set、有参、无参、toString、hashcode、equal（放在字段上则只生成字段的）
@Builder
@Singular
@Delegate
@Value
@Accessors
@Wither
@SneakyThrows
```



#### 12、 复杂查询环境搭建

##### 12.1多对一处理

SQL：

```sql
CREATE TABLE `teacher` (
    `id` INT(10) NOT NULL,
    `name` VARCHAR(30) DEFAULT NULL,
    PRIMARY KEY(`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf-8

INSERT INTO teacher(`id`,`name`) VALUES (1,'秦老师');

CREATE TABLE `student` (
    `id` INT(10) NOT NULL,
    `name` VARCHAR(30) DEFAULT NULL,
    `tid` INT(10) DEFAULT NULL,
    PRIMARY KEY(`id`),
    KEY `fktid` (`tid`),
    CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO student(`id`,`name`,`tid`) VALUES (`1`,'秦老师',`1`);
INSERT INTO student(`id`,`name`,`tid`) VALUES (`2`,'秦老师',`1`);
INSERT INTO student(`id`,`name`,`tid`) VALUES (`3`,'秦老师',`1`);
INSERT INTO student(`id`,`name`,`tid`) VALUES (`4`,'秦老师',`1`);
INSERT INTO student(`id`,`name`,`tid`) VALUES (`5`,'秦老师',`1`);
```

环境搭建：

1. 导入lombok

2. 新建实体类Teacher，Student

   - ```Java
     @Data
     public class Student {
         private int id;
         private String name;
         //学生关联一个老师
         private Teacher teacher;
     }
     ```

   - ```Java
     @Data
     public class Teacher {
         private int id;
         private String name;
     }
     ```

3. 建立mapper接口

   - ```java
     public interface StudentMapper {
     
     }
     ```

   - ```Java
     public interface TeacherMapper {
         @Select("select * from teacher where id = #{tid}")
         Teacher getTeacher(@Param("tid") int id);
     }
     ```

4. 建立mapper.xml文件

   - ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE mapper
             PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
             "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
     <!--核心配置文件-->
     <mapper namespace="com.daryl.dao.StudentMapper">
     
     </mapper>
     ```

   - ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE mapper
             PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
             "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
     <!--核心配置文件-->
     <mapper namespace="com.daryl.dao.TeacherMapper">
     
     </mapper>
     ```

5. 在核心配置文件中绑定mapper接口

   - ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE configuration
             PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
                     "http://mybatis.org/dtd/mybatis-3-config.dtd">
             <!--核心配置文件-->
     <configuration>
     
         <properties resource="db.properties"/>
     
         <settings>
             <setting name="logImpl" value="STDOUT_LOGGING"/>
         </settings>
     
     <!--配置环境-->
     <environments default="development">
         <environment id="development">
             <transactionManager type="JDBC"/><!--事务管理-->
             <dataSource type="POOLED">
                 <property name="driver" value="${driver}"/>
                 <property name="url" value="${url}"/>
                 <property name="username" value="${username}"/>
                 <property name="password" value="${pwd}"/>
             </dataSource>
         </environment>
     </environments>
     
         <mappers>
             <mapper class="com.daryl.dao.TeacherMapper" />
             <mapper class="com.daryl.dao.StudentMapper" />
         </mappers>
     
     </configuration>
     ```

6. 测试

   - ```Java
     public class Test {
         public static void main(String[] args) {
             SqlSession sqlSession = MybatisUtils.getSqlSession();
             TeacherMapper mapper = sqlSession.getMapper(TeacherMapper.class);
             Teacher teacher = mapper.getTeacher(1);
             System.out.println(teacher);
     
             sqlSession.close();
         }
     }
     ```




#### 13、mybatis中$与#的区别？

- #{} 占位符，**对应的变量自动加上单引号** ，能防止sql 注入
- ${}  拼接符

1. 不论是单个参数，还是多个参数，一律都<u>建议使用注解@Param("")</u>
2. 能用 #{} 的地方就用 #{}，<u>不用或少用 ${}</u>
3. <u>表名作参数时，必须用 ${}</u>，不然生成后sql会加上单引号。如：select * from ${tableName}
4. order by 时，必须用 ${}。如：select * from t_user order by ${columnName}
5. 使用 ${} 时，要注意何时加或不加单引号，即 ${} 和 '${}'

注：@Param("") 是 @Param(value="") 的简写

### Mybatis动态sql

#### 1、`foreach`

在sql中对集合进行迭代。

```xml
<delete id="deleteBatch"> 
　　　　delete from user where id in
　　　　<foreach collection="array" item="id" index="index" open="(" close=")" separator=",">
　　　　　　#{id}
　　　　</foreach>
　　</delete>
```

> 　　我们假如说参数为----  int[] ids = {1,2,3,4,5}  ----那么打印之后的SQL如下：
>
> 　　`delete form user where id in (1,2,3,4,5)`
>

　　释义：

​		你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象传递给 foreach 作为集合参数。当使用可迭代对象或者数组时，index 是当前迭代的次数，item 的值是本次迭代获取的元素。<u>当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。</u>

　　　　`collection` ：collection属性的值有三个分别是list、array、map三种，分别对应的参数类型为：List、数组、map集合，我在上面传的参数为数组，所以值为array

　　　　`item` ： 表示在迭代过程中每一个元素的别名

　　　　`index` ：表示在迭代过程中每次迭代到的位置（下标）

　　　　`open` ：前缀

　　　　`close` ：后缀

　　　　`separator` ：分隔符，表示迭代时每个元素之间以什么分隔我们通常可以将之用到批量删除、添加等操作中。

（1）如果传入的是单参数且参数类型是一个List的时候，collection属性值为list
（2）如果传入的是单参数且参数类型是一个array数组的时候，collection的属性值为array
（3）如果传入的参数是多个的时候，我们就需要把它们封装成一个Map了，当然单参数也可以封装成Map，实际上如果你在传入参数的时候，在mybatis里面也是会把它封装成一个Map的，map的key就是参数名，所以这个时候collection属性值就是传入的List或array对象在自己封装的map里面的key

下面分别来看看上述三种情况的示例代码：

**单参数List的类型**：

```xml
<select id="getBlogs" parameterType="java.util.List" resultType="Blog">
    select * from t_blog where id in
    <foreach collection="list" index="index" item="item" open="(" separator="," close=")">
                #{item}
    </foreach>
</select>
```

上述collection的值为list，对应的Mapper是这样的：

```java
public List getBlogs(List ids);
```

测试代码如下所示：

```java
SqlSession sqlSession = sqlSessionFactory.openSession();
BlogMapper blogMapper = sqlSession.getMapper(BlogMapper.class);
List ids = new ArrayList();
ids.add(1);
ids.add(3);
ids.add(6);
List blogs = blogMapper.getBlogs(ids);
for (Blog blog : blogs)
{
    System.out.println(blog);
}
sqlSession.close();
```

**单参数array数组的类型：**

```xml
<select id="getBlogs" parameterType="java.util.ArrayList" resultType="Blog">
    select * from t_blog where id in
    <foreach collection="array" index="index" item="item" open="(" separator="," close=")">
        #{item}
    </foreach>
</select> 
```

上述collection为array，对应的Mapper代码：

```java
public List getBlogs(int[] ids);
```

对应的测试代码：

```java
SqlSession sqlSession = sqlSessionFactory.openSession();
BlogMapper blogMapper = session.getMapper(BlogMapper.class);
int[] ids = new int[] {1,3,6,9};
List blogs = blogMapper.getBlogs(ids);
for (Blog blog : blogs)
{
    System.out.println(blog);
}
session.close();
```

**自己把参数封装成Map的类型：**

```xml
<select id="getBlogs" parameterType="java.util.HashMap" resultType="Blog">
    select * from t_blog where title like "%"#{title}"%" and id in
    <foreach collection="ids" index="index" item="item" open="(" separator="," close=")">
        #{item}
    </foreach>
</select>
```

上述collection的值为ids，是传入的参数Map的key，对应的Mapper代码：

```java
public List getBlogs(Map params);
```

对应测试代码：

```java
SqlSession sqlSession = sqlSessionFactory.openSession();
BlogMapper blogMapper = session.getMapper(BlogMapper.class);
final List ids = new ArrayList();
ids.add(1);
ids.add(2);
ids.add(3);
ids.add(6);
ids.add(7);
ids.add(9);
Map params = new HashMap();
params.put("ids", ids);
params.put("title", "历史");
List blogs = blogMapper.getBlogs(params);
for (Blog blog : blogs)
{
    System.out.println(blog);
}
session.close();
```

#### 2、collection（多个参数）

collection标签是集合标签，它与association关联标签几乎是一样的，它们相似的程度之高，常常让人产生误解。关联association标签处理“一对一”类型的关系，例如：一个博客有一个用户。

```java
public Class Blog 
{
    private Author author;
}
```

但一个博客有很多文章（Post)，collection标签处理“一对多”类型的关系，这可以用下面的写法来表示：

```java
public Class Blog 
{
    private List<Post> posts;
}
```

collection标签和association标签的用法非常相似，区别在于ofType属性：

```xml
<association property="author" javaType="Author">

<collection property="posts" javaType="ArrayList" ofType="Post" />
```

##### 附注

- Dao层使用`@Param` 注解时

```xml
#Dao层使用`@Param` 注解时
User select(@Param("username") String username,@Param("password") String password);
#xml文件内部的值可以写成username、password，更加直观
<select id="select" resultType="user">
	select *
    from tb_user
    where 
    	username=#{username}
    	and password=#{password}
</select>
```

- #Dao层不使用`@Param` 注解时

```xml
#Dao层不使用`@Param` 注解时
User select(String username,String password);

#xml文件内部的值可以写成arg0、arg1或者param1、param2
<select id="select" resultType="user">
	select *
    from tb_user
    where 
    	username=#{arg0}
    	and password=#{arg1}
</select>
或者
<select id="select" resultType="user">
	select *
    from tb_user
    where 
    	username=#{param1}
    	and password=#{param2}
</select>
```

> 结论：以后接口参数是多个时，在每个参数上都使用 `@Param` 注解。这样代码的可读性更高。

#### 3、`trim`

```xml
select * from user 

　　<trim prefix="WHERE" prefixoverride="AND |OR">

　　　　<if test="name != null and name.length()>0"> AND name=#{name}</if>

　　　　<if test="gender != null and gender.length()>0"> AND gender=#{gender}</if>

　　</trim>
```

　　假如说name和gender的值都不为null的话打印的SQL为：select * from user where 空格  name = 'xx' and gender = 'xx'

　　在空格的地方是不存在第一个and的，上面两个属性的意思如下：

　　`prefix`：前缀　　　　　　

　　`prefixoverride`：去掉第一个and或者是or

​		`suffixoverride`：去掉最后一个符号

​		`suffix`：后缀

#### 4、sql复用resultType、resultMap

将需要复用的SQL片段抽取到 sql 标签中

```xml
<sql id="brand_column">
	id, brand_name as brandName, company_name as companyName, ordered, description, status
</sql>
```
#####  在原sql语句中进行引用resultType

 使用 include 标签引用上述的 SQL 片段，而 refid 指定上述 SQL 片段的id值。

 ```xml
 <select id="selectAll" resultType="brand">
    select
    <include refid="brand_column" />
    from tb_brand;
</select>
 ```
#####  方法二：resultMap

 起别名 + sql片段的方式可以解决上述问题，但是它也存在问题。如果还有功能只需要查询部分字段，而不是查询所有字段，那么我们就需要再定义一个 SQL 片段，这就显得不是那么灵活。

那么我们也可以使用resultMap来定义字段和属性的映射关系的方式解决上述问题。

~~~xml
在映射配置文件中使用resultMap定义 字段 和 属性 的映射关系
```xml
<resultMap id="brandResultMap" type="brand">
<!--
        id：完成主键字段的映射
            column：表的列名
            property：实体类的属性名
        result：完成一般字段的映射
            column：表的列名
            property：实体类的属性名
    -->
<result column="brand_name" property="brandName"/>
<result column="company_name" property="companyName"/>
#注意：在上面只需要定义 字段名 和 属性名 不一样的映射，而一样的则不需要专门定义出来。
</resultMap>
~~~
SQL语句正常编写

```xml
<select id="selectAll" resultMap="brandResultMap">
    select *
    from tb_brand;
</select>
```

####     5、if：条件判断

```xml
<select id="selectByCondition" resultMap="brandResultMap">
    select *
    from tb_brand
    where
        <if test="status != null">
            and status = #{status}
        </if>
        <if test="companyName != null and companyName != '' ">
            and company_name like #{companyName}
        </if>
        <if test="brandName != null and brandName != '' ">
            and brand_name like #{brandName}
        </if>
</select>
```

如果第一个值status没有值则会出现`select * from tb_brand where and company_name like ? and brand_name like ?`的情况，where直接接了and，因此需要下面的where标签。

#### 6、where

- 替换where关键字
- 会动态的去掉第一个条件前的 and
- 如果所有的参数没有值则不加where关键字

```xml
<select id="selectByCondition" resultMap="brandResultMap">
    select *
    from tb_brand
    <where>
        <if test="status != null">
            and status = #{status}
        </if>
        <if test="companyName != null and companyName != '' ">
            and company_name like #{companyName}
        </if>
        <if test="brandName != null and brandName != '' ">
            and brand_name like #{brandName}
        </if>
    </where>
</select>
```

> 注意：需要给每个条件前都加上 and 关键字。

#### 7、choose单个条件生效

```xml
<select id="selectByConditionSingle" resultMap="brandResultMap">
    select *
    from tb_brand
    <where>
        <choose><!--相当于switch-->
            <when test="status != null"><!--相当于case-->
                status = #{status}
            </when>
            <when test="companyName != null and companyName != '' "><!--相当于case-->
                company_name like #{companyName}
            </when>
            <when test="brandName != null and brandName != ''"><!--相当于case-->
                brand_name like #{brandName}
            </when>
        </choose>
    </where>
</select>
```

#### 8、注解动态sql

```java
String sql = new SQL() {{
select("P.id,P.password,P.username");
if(id!=null) {
where("P.id like #{id}");
if(username!=null) {
where("P.username like #{username}");
}
}}.toString();
```

