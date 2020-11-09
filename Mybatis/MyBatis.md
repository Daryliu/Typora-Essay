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

   - 编写一个工具类，从xml中构建SqlSessionFactory；（sqlSessionFactory用于创建sqLSession，而sqLSession相当于JDBC中的statement，执行sql操作用）

   ```Java
   //sqlSessionFactory用于创建sqLSession，而sqLSession相当于JDBC中的statement，执行sql操作用
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

- 接口实现类（由原来的UserDaoImpl转换为一个Mapper配置文件）**连接了Dao接口和实体类**

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
<insert id="addUser2" parameterType="map">
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

    - ```java
      log4j.rootLogger=INFO,consoleAppender,logfile,MAIL			//日志输出的位置
      log4j.addivity.org.apache=true
      #ConsoleAppender，控制台输出
      #FileAppender，文件日志输出
      #SMTPAppender，发邮件输出日志
      #SocketAppender，Socket 日志
      #NTEventLogAppender，Window NT 日志
      #SyslogAppender，
      #JMSAppender，
      #AsyncAppender，
      #NullAppender
      #文件输出：RollingFileAppender
      #log4j.rootLogger = INFO,logfile
      log4j.appender.logfile = org.apache.log4j.RollingFileAppender
      log4j.appender.logfile.Threshold = INFO
      # 输出以上的 INFO 信息
      log4j.appender.logfile.File = INFO_log.html
      #保存 log 文件路径
      Log4j 从入门到详解
      10
      log4j.appender.logfile.Append = true		//日志输出到文件
      # 默认为 true，添加到末尾，false 在每次启动时进行覆盖
      log4j.appender.logfile.MaxFileSize = 1MB		//日志切割
      # 一个 log 文件的大小，超过这个大小就又会生成 1 个日志 # KB ，MB，GB
      log4j.appender.logfile.MaxBackupIndex = 3		//最多保存三个备份
      # 最多保存 3 个文件备份
      log4j.appender.logfile.layout = org.apache.log4j.HTMLLayout
      # 输出文件的格式
      log4j.appender.logfile.layout.LocationInfo = true
      #是否显示类名和行数
      log4j.appender.logfile.layout.Title
      =title:\u63d0\u9192\u60a8\uff1a\u7cfb\u7edf\u53d1\u751f\u4e86\u4e25\u91cd\u9519\u8b
      ef
      #html 页面的 < title >
      ############################## SampleLayout ####################################
      # log4j.appender.logfile.layout = org.apache.log4j.SampleLayout
      ############################## PatternLayout ###################################
      # log4j.appender.logfile.layout = org.apache.log4j.PatternLayout
      # log4j.appender.logfile.layout.ConversionPattern =% d % p [ % c] - % m % n % d
      ############################## XMLLayout #######################################
      # log4j.appender.logfile.layout = org.apache.log4j.XMLLayout
      # log4j.appender.logfile.layout.LocationInfo = true #是否显示类名和行数
      ############################## TTCCLayout ######################################
      # log4j.appender.logfile.layout = org.apache.log4j.TTCCLayout
      # log4j.appender.logfile.layout.DateFormat = ISO8601
      #NULL, RELATIVE, ABSOLUTE, DATE or ISO8601.
      # log4j.appender.logfile.layout.TimeZoneID = GMT - 8 : 00
      # log4j.appender.logfile.layout.CategoryPrefixing = false ##默认为 true 打印类别名
      # log4j.appender.logfile.layout.ContextPrinting = false ##默认为 true 打印上下文信息
      # log4j.appender.logfile.layout.ThreadPrinting = false ##默认为 true 打印线程名
      # 打印信息如下：
      #2007 - 09 - 13 14 : 45 : 39 , 765 [http - 8080 - 1 ] ERROR com.poxool.test.test -
      error 成功关闭链接
      ###############################################################################
      #每天文件的输出：DailyRollingFileAppender
      #log4j.rootLogger = INFO,errorlogfile
      log4j.appender.errorlogfile = org.apache.log4j.DailyRollingFileAppender
      log4j.appender.errorlogfile.Threshold = ERROR
      log4j.appender.errorlogfile.File = ../logs/ERROR_log
      log4j.appender.errorlogfile.Append = true
      #默认为 true，添加到末尾，false 在每次启动时进行覆盖
      log4j.appender.errorlogfile.ImmediateFlush = true
      #直接输出，不进行缓存
      # ' . ' yyyy - MM: 每个月更新一个 log 日志
      # ' . ' yyyy - ww: 每个星期更新一个 log 日志
      # ' . ' yyyy - MM - dd: 每天更新一个 log 日志
      # ' . ' yyyy - MM - dd - a: 每天的午夜和正午更新一个 log 日志
      # ' . ' yyyy - MM - dd - HH: 每小时更新一个 log 日志
      # ' . ' yyyy - MM - dd - HH - mm: 每分钟更新一个 log 日志
      Log4j 从入门到详解
      11
      log4j.appender.errorlogfile.DatePattern = ' . ' yyyy - MM - dd ' .log '		//日志名称的格式
      #文件名称的格式
      log4j.appender.errorlogfile.layout = org.apache.log4j.PatternLayout
      log4j.appender.errorlogfile.layout.ConversionPattern =%d %p [ %c] - %m %n %d
      #控制台输出：
      #log4j.rootLogger = INFO,consoleAppender
      log4j.appender.consoleAppender = org.apache.log4j.ConsoleAppender
      log4j.appender.consoleAppender.Threshold = ERROR
      log4j.appender.consoleAppender.layout = org.apache.log4j.PatternLayout
      log4j.appender.consoleAppender.layout.ConversionPattern =%d %-5p %m %n
      log4j.appender.consoleAppender.ImmediateFlush = true
      # 直接输出，不进行缓存
      log4j.appender.consoleAppender.Target = System.err
      # 默认是 System.out 方式输出
      #发送邮件：SMTPAppender
      #log4j.rootLogger = INFO,MAIL
      log4j.appender.MAIL = org.apache.log4j.net.SMTPAppender
      log4j.appender.MAIL.Threshold = INFO
      log4j.appender.MAIL.BufferSize = 10
      log4j.appender.MAIL.From = yourmail@gmail.com
      log4j.appender.MAIL.SMTPHost = smtp.gmail.com
      log4j.appender.MAIL.Subject = Log4J Message
      log4j.appender.MAIL.To = yourmail@gmail.com
      log4j.appender.MAIL.layout = org.apache.log4j.PatternLayout
      log4j.appender.MAIL.layout.ConversionPattern =%d - %c -%-4r [%t] %-5p %c %x - %m %n
      #数据库：JDBCAppender
      log4j.appender.DATABASE = org.apache.log4j.jdbc.JDBCAppender
      log4j.appender.DATABASE.URL = jdbc:oracle:thin:@ 210.51 . 173.94 : 1521 :YDB
      log4j.appender.DATABASE.driver = oracle.jdbc.driver.OracleDriver
      log4j.appender.DATABASE.user = ydbuser
      log4j.appender.DATABASE.password = ydbuser
      log4j.appender.DATABASE.sql = INSERT INTO A1 (TITLE3) VALUES ( ' %d - %c %-5p %c %x - %m%n
      ' )
      log4j.appender.DATABASE.layout = org.apache.log4j.PatternLayout
      log4j.appender.DATABASE.layout.ConversionPattern =% d - % c -%- 4r [ % t] %- 5p % c %
      x - % m % n
      #数据库的链接会有问题，可以重写 org.apache.log4j.jdbc.JDBCAppender 的 getConnection() 使用数
      据库链接池去得链接，可以避免 insert 一条就链接一次数据库
      ```

      