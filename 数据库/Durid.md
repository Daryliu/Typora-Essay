[TOC]

# 从JDBC理解Durid

- 各数据库厂商使用相同的接口，Java代码不需要针对不同数据库分别开发
- 可随时替换底层数据库，访问数据库的Java代码基本不变

## 1、代码编写步骤

### 1.1注册驱动

`Class.forName("com.mysql.jdbc.Driver");`

### 1.2获取连接

`Connection conn = DriverManager.getConnection(url, username, password);`

Java代码需要发送SQL给MySQL服务端，就需要先建立连接

### 1.3定义SQL语句

`String sql =  “update…” ;`

### 1.4获取执行SQL对象

执行SQL语句需要SQL执行对象，而这个执行对象就是Statement对象

`Statement stmt = conn.createStatement();`

### 1.5执行SQL

`stmt.executeUpdate(sql);`  

### 1.6处理返回结果

### 1.7释放资源

> 实际代码如下：

```java
/**
 * JDBC快速入门
 */
public class JDBCDemo {

    public static void main(String[] args) throws Exception {
        //1. 注册驱动
        //Class.forName("com.mysql.jdbc.Driver");
        //2. 获取连接
        String url = "jdbc:mysql://127.0.0.1:3306/db1";
        String username = "root";
        String password = "1234";
        Connection conn = DriverManager.getConnection(url, username, password);
        //3. 定义sql
        String sql = "update account set money = 2000 where id = 1";
        //4. 获取执行sql的对象 Statement
        Statement stmt = conn.createStatement();
        //或者 预编译SQL的执行SQL对象：防止SQL注入  PreparedStatement  prepareStatement(sql)
        //5. 执行sql
        int count = stmt.executeUpdate(sql);//受影响的行数
        //6. 处理结果
        System.out.println(count);
        //7. 释放资源
        stmt.close();
        conn.close();
    }
}
```

## 2、ResultSet

执行DQL 语句，返回 ResultSet 对象，可以从 `ResultSet` 对象中获取我们想要的数据。

> boolean next()
>
> ​		将光标从当前位置向前移动一行
> ​		判断当前行是否为有效行
> 方法返回值说明：
>
> ​		true ： 有效航，当前行有数据
> ​		false ： 无效行，当前行没有数据
>
> xxx getXxx(参数)：获取数据
>
> ​		xxx : 数据类型；如： int getInt(参数) ；String getString(参数)
> ​		参数
> ​				int类型的参数：列的编号，从1开始
> ​				String类型的参数： 列的名称

实际数据库中的结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/4c9444fff57f4b4396ca4b585d68d195.png)

一开始光标指定于第一行前，如图所示红色箭头指向于表头行。当我们<u>调用了 next() 方法后，光标就下移到第一行数据，并且方法返回true</u>，此时就可以通过 getInt("id") 获取当前行id字段的值，也可以通过 getString("name") 获取当前行name字段的值。如果想获取下一行的数据，继续调用 next() 方法，以此类推。

```java
/**
  * 查询account账户表数据，封装为Account对象中，并且存储到ArrayList集合中
  * 1. 定义实体类Account
  * 2. 查询数据，封装到Account对象中
  * 3. 将Account对象存入ArrayList集合中
  */
@Test
public void testResultSet2() throws  Exception {
    //1. 注册驱动
    //Class.forName("com.mysql.jdbc.Driver");
    //2. 获取连接：如果连接的是本机mysql并且端口是默认的 3306 可以简化书写
    String url = "jdbc:mysql:///db1?useSSL=false";
    String username = "root";
    String password = "1234";
    Connection conn = DriverManager.getConnection(url, username, password);

    //3. 定义sql
    String sql = "select * from account";

    //4. 获取statement对象
    Statement stmt = conn.createStatement();

    //5. 执行sql
    ResultSet rs = stmt.executeQuery(sql);

    // 创建集合
    List<Account> list = new ArrayList<>();
   
    // 6.1 光标向下移动一行，并且判断当前行是否有数据
    while (rs.next()){
        Account account = new Account();

        //6.2 获取数据  getXxx()
        int id = rs.getInt("id");
        String name = rs.getString("name");
        double money = rs.getDouble("money");

        //赋值
        account.setId(id);
        account.setName(name);
        account.setMoney(money);

        // 存入集合
        list.add(account);
    }

    System.out.println(list);

    //7. 释放资源
    rs.close();
    stmt.close();
    conn.close();
}
```

## 3、PreparedStatement

> PreparedStatement作用：
>
> - 预编译SQL语句并执行：预防SQL注入问题

#### SQL注入

**SQL注入**是通过操作输入来修改事先定义好的SQL语句，用以达到执行代码对服务器进行攻击的方法。

```java
 @Test
public void testPreparedStatement() throws  Exception {
    //2. 获取连接：如果连接的是本机mysql并且端口是默认的 3306 可以简化书写
    String url = "jdbc:mysql:///db1?useSSL=false";
    String username = "root";
    String password = "1234";
    Connection conn = DriverManager.getConnection(url, username, password);

    // 接收用户输入 用户名和密码
    String name = "zhangsan";
    String pwd = "' or '1' = '1";

    // 定义sql
    String sql = "select * from tb_user where username = ? and password = ?";
    // 获取pstmt对象
    PreparedStatement pstmt = conn.prepareStatement(sql);
    // 设置？的值
    pstmt.setString(1,name);
    pstmt.setString(2,pwd);
    // 执行sql
    ResultSet rs = pstmt.executeQuery();
    // 判断登录是否成功
    if(rs.next()){
        System.out.println("登录成功~");
    }else{
        System.out.println("登录失败~");
    }
    //7. 释放资源
    rs.close();
    pstmt.close();
    conn.close();
}
```

> 小结：
>
> - 在获取PreparedStatement对象时，将sql语句发送给mysql服务器进行检查，编译（这些步骤很耗时）
> - 执行时就不用再进行这些步骤了，速度更快
> - 如果sql模板一样，则只需要进行一次检查、编译

## 4、数据库连接池

> - 数据库连接池是个容器，负责分配、管理数据库连接(Connection)
>
> - 它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个；
>
> - 释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏
>
> - 好处
>
>   资源重用
>   提升系统响应速度
>   避免数据库连接遗漏

之前我们代码中使用连接是没有使用都创建一个Connection对象，使用完毕就会将其销毁。这样重复创建销毁的过程是特别耗费计算机的性能的及消耗时间的。

连接池是在一开始就创建好了一些连接（Connection）对象存储起来。用户需要连接数据库时，不需要自己创建连接，而只需要从连接池中获取一个连接进行使用，使用完毕后再将连接对象归还给连接池；这样就可以起到资源重用，也节省了频繁创建连接销毁连接所花费的时间，从而提升了系统响应的速度。

#### 实现

以后就不需要通过 `DriverManager` 对象获取 `Connection` 对象，而是通过连接池（DataSource）获取 `Connection` 对象。

##### Driud使用

> - 导入jar包 druid-1.1.12.jar
> - 定义配置文件
> - 加载配置文件
> - 获取数据库连接池对象
> - 获取连接

###### druid.properties文件

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql:///db1?useSSL=false&useServerPrepStmts=true
username=root
password=1234
# 初始化连接数量
initialSize=5
# 最大连接数
maxActive=10
# 最大等待时间
maxWait=3000
```

###### 连接数据库的代码

```Java
/**
 * Druid数据库连接池演示
 */
public class DruidDemo {

    public static void main(String[] args) throws Exception {
        //1.导入jar包
        //2.定义配置文件
        //3. 加载配置文件
        Properties prop = new Properties();
        prop.load(new FileInputStream("jdbc-demo/src/druid.properties"));
        //4. 获取连接池对象
        DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);

        //5. 获取数据库连接 Connection
        Connection connection = dataSource.getConnection();
        System.out.println(connection); //获取到了连接后就可以继续做其他操作了

        //System.out.println(System.getProperty("user.dir"));
    }
}
```

