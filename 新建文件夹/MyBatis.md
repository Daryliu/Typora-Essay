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



