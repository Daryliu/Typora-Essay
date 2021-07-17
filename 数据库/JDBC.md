### <font color="redblack">JDBC(:通过JAVA访问数据库)</font>

##### <font color="greenyellow">[MySql的JDBC连接](https://how2j.cn/k/jdbc/jdbc-statement/387.html#nowhere)</font>

```java
public static void main(String[] args) {
  
        try {
            //1     初始化数据库驱动
            Class.forName("com.mysql.jdbc.Driver");
  
            // 2    建立与数据库的Connection连接
            // 这里需要提供：
            // 数据库所处于的ip:127.0.0.1 (本机)
            // 数据库的端口号： 3306 （mysql专用端口号）
            // 数据库名称 how2java
            // 编码方式 UTF-8
            // 账号 root
            // 密码 admin
            Connection c = DriverManager
                    .getConnection(
                            "jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8",
                            "root", "admin");
  
            System.out.println("连接成功，获取连接对象： " + c);
            //3       Statement是用于执行SQL语句的，比如增加，删除
            Statement s = c.createStatement();
            
            //4       execute执行sql语句
            String sql = "insert into hero values(null,"+"'提莫'"+","+313.0f+","+50+")";//插入
            s.execute(sql);
  
            System.out.println("执行插入语句成功");
        } catch (ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }finally {***************5  关闭连接先1 关闭Statement后 2 关闭Connection*******************
            // 数据库的连接时有限资源，相关操作结束后，养成关闭数据库的好习惯
            // 先关闭Statement   执行sql语句的Statement还存在的话就关闭
            if (s != null)
                try {
                    s.close();
                } catch (SQLException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            // 后关闭Connection       连接还存在就关闭
            if (c != null)
                try {
                    c.close();
                } catch (SQLException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
 
        }
  
    }
**************************************************自动关闭**********************************************************
    public static void main(String[] args) {
  
        try {
            Class.forName("com.mysql.jdbc.Driver");//初始化数据库驱动
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
  
        try (//创建数据库连接
            Connection c = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8",
                "root", "admin");
            //创建用于执行SQL语句的Statement
            Statement s = c.createStatement();             
        )
        {
            String sql = "insert into hero values(null," + "'提莫'" + "," + 313.0f + "," + 50 + ")";//插入操作
            s.execute(sql);//执行操作
              
        } catch (SQLException e) {//		自动关闭
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
```

##### <font color="yellow">增、删、改</font>

```java
public static void main(String[] args) {
   
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
   
        try (
            Connection c = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8",
                "root", "admin");
            Statement s = c.createStatement();             
        )
        {
            //1                jdbc增加  ||  插入			// 注意： 字符串要用单引号'
            String sql = "insert into hero values(null," + "'提莫'" + "," + 313.0f + "," + 50 + ")";
            //2                jdbc删除
            String sql = "delete from hero where id = 5";
            //3                jdbc修改
            String sql = "update hero set name = 'name 5' where id = 3";
            s.execute(sql);
               
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
```

##### <font color="greenyellow">查</font>

###### ResultSet的getInt(),getString()方法

通过索引或者列名来获得查询结果集中的某一列的值

```java
public static void main(String[] args) {
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
 
        try (Connection c = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8",
                "root", "admin"); 
             Statement s = c.createStatement();) {
 			//4            查*********************************************************************
            String sql = "select * from hero";
            
            //select count(*) from hero查询hero中的数据数量******************
            
            // 执行查询语句，并把结果集返回给ResultSet
            ResultSet rs = s.executeQuery(sql);
            while (rs.next()) {		//判断查询到之后用下面定义的值获取查询到的值**************************
                int id = rs.getInt("id");// 可以使用字段名
                String name = rs.getString(2);// 也可以使用字段的顺序
                float hp = rs.getFloat("hp");
                int damage = rs.getInt(4);
                System.out.printf("%d\t%s\t%f\t%d%n", id, name, hp, damage);
            }
            // 不一定要在这里关闭ReultSet，因为Statement关闭的时候，会自动关闭ResultSet
            // rs.close();
 
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
```

```java
//jdbc中的limit实现分页查询
//limit arg1,arg2 arg1指定查询记录的起始位置；arg2用于指定查询数据所返回的记录数
select * from tablename limit 0,1
即取出第一条记录。

select * from tablename limit 1,1
第二条记录

select * from tablename limit 10,20
从第11条到31条（共计20条）

select * from tablename limit 0,2
取出2条数据 第一条和第二条
```

---

#### <font color="yellowgreen">Statement和PreparedStatement</font>

区别：

PreparedStatement有预编译的过程，已经绑定sql，之后无论执行多少遍，都不会再去进行编译，

而 statement 不同，如果执行多变，则相应的就要编译多少遍sql，所以从这点看，preStatement 的效率会比 Statement要高一些.

preStatement是预编译的，所以可以有效的防止 SQL注入等问题



和 Statement一样，PreparedStatement也是用来**执行sql语句**的；需要根据sql语句创建PreparedStatement
除此之外，还能够通过设置参数，指定相应的值，而不是Statement那样使用字符串拼接

```java
public static void main(String[] args) {
  
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
  
        String sql = "insert into hero values(null,?,?,?)";//第一步		PreparedStatement
        try (Connection c = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8","root", "admin");
            Statement s = c.createStatement();//Statement第<1>步
            PreparedStatement ps = c.prepareStatement(sql);//第二步		PreparedStatement速度快
        ) {
            // Statement需要进行字符串拼接，可读性和维修性比较差
            String sql0 = "insert into hero values(null," + "'提莫'" + "," + 313.0f + "," + 50 + ")";//Statement第<2>步
            s.execute(sql0);
  
            // PreparedStatement 使用参数设置，可读性好，不易犯错
            // "insert into hero values(null,?,?,?)";
            ps.setString(1, "提莫");
            ps.setFloat(2, 313.0f);
            ps.setInt(3, 50);
            ps.execute();
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
  
    }
```

假设name是用户提交来的数据   String name = "'盖伦' OR 1=1";

使用Statement就需要进行字符串拼接
拼接出来的语句是：select * from hero where name = '盖伦' OR 1=1
因为有OR 1=1，这是恒成立的
那么就会把所有的英雄都查出来，而不只是盖伦
如果Hero表里的数据是海量的，比如几百万条，**把这个表里的数据全部查出来**
会让数据库负载变高，CPU100%，内存消耗光，**响应变得极其缓慢**

而PreparedStatement使用的是**参数设置**，就不会有这个问题

```java
// 假设name是用户提交来的数据
            String name = "'盖伦' OR 1=1";
            String sql0 = "select * from hero where name = " + name;//PreparedStatement
            // 拼接出来的SQL语句就是
            // select * from hero where name = '盖伦' OR 1=1      //Statement
            // 因为有OR 1=1，所以恒成立
            // 那么就会把所有的英雄都查出来，而不只是盖伦
            // 如果Hero表里的数据是海量的，比如几百万条，把这个表里的数据全部查出来
            // 会让数据库负载变高，CPU100%，内存消耗光，响应变得极其缓慢
```

---

#### <font color="yellow">Exrcute与ExrcuteUpdate的区别</font>

相同点：都可以执行增加，删除，修改

不同1：
execute**可以执行查询语句**
然后通过getResultSet，把结果集取出来
executeUpdate**不能执行查询语句**
不同2:
execute**返回boolean类型**，true表示执行的是查询语句，false表示执行的是insert,delete,update等等
executeUpdate**返回的是int**，表示有多少条数据受到了影响

```java
public static void main(String[] args) {
  
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
 
        try (Connection c = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8","root", "admin");
            Statement s = c.createStatement();) {
  
            // 不同1：execute可以执行查询语句
            // 然后通过getResultSet，把结果集取出来
            String sqlSelect = "select * from hero";
  
            s.execute(sqlSelect);
            ResultSet rs = s.getResultSet();
            while (rs.next()) {
                System.out.println(rs.getInt("id"));
            }
  
            // executeUpdate不能执行查询语句
            // s.executeUpdate(sqlSelect);
  
            // 不同2:
            // execute返回boolean类型，true表示执行的是查询语句，false表示执行的是insert,delete,update等等
            boolean isSelect = s.execute(sqlSelect);
            System.out.println(isSelect);
  
            // executeUpdate返回的是int，表示有多少条数据受到了影响
            String sqlUpdate = "update Hero set hp = 300 where id < 100";
            int number = s.executeUpdate(sqlUpdate);
            System.out.println(number);
  
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
  
    }
```

***

#### <font color="yellow">获取自增长ID以及表的元数据</font>

JDBC通过**getGeneratedKeys**获取该id

```java
public static void main(String[] args) {
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
         String sql = "insert into hero values(null,?,?,?)";
        try (Connection c = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8","root", "admin");
             //加了个Statement.RETURN_GENERATED_KEYS参数，以确保会返回自增长ID。 通常情况下不需要加这个，有的时候需要加，所以先加上，保险一些
                PreparedStatement ps = c.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);          
                ) {
  
            ps.setString(1, "盖伦");
            ps.setFloat(2, 616);
            ps.setInt(3, 100);
   
            // 执行插入语句
            ps.execute();
   
            // 在执行完插入语句后，MySQL会为新插入的数据分配一个自增长id
            
            ResultSet rs = ps.getGeneratedKeys();// JDBC通过getGeneratedKeys获取该id
            if (rs.next()) {
                int id = rs.getInt(1);//相当于rs.getInt(id);
                System.out.println(id);
            }
   
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
   
    }
```

###### 元数据

含义：和数据库服务器相关的数据，比如数据库版本，有哪些表，表有哪些字段，字段类型是什么等等

```java
			// 查看数据库层面的元数据
            // 即数据库服务器版本，驱动版本，都有哪些数据库等等
            DatabaseMetaData dbmd = c.getMetaData();
  
            // 获取数据库服务器产品名称
            System.out.println("数据库产品名称:\t"+dbmd.getDatabaseProductName());
            // 获取数据库服务器产品版本号
            System.out.println("数据库产品版本:\t"+dbmd.getDatabaseProductVersion());
            // 获取数据库服务器用作类别和表名之间的分隔符 如test.user
            System.out.println("数据库和表分隔符:\t"+dbmd.getCatalogSeparator());
            // 获取驱动版本
            System.out.println("驱动版本:\t"+dbmd.getDriverVersion());
  
            System.out.println("可用的数据库列表：");
            // 获取数据库名称
            ResultSet rs = dbmd.getCatalogs();
            while (rs.next()) {
                System.out.println("数据库名称:\t"+rs.getString(1));
  	          }
  
```

***

#### <font color="yellow">jdbc中的事务</font>

1>在事务中的多个操作，**要么都成功，要么都失败**
2>通过 c.setAutoCommit(false);**关闭自动提交**
3>使用 c.commit();进行**手动提交**

**MYSQL 表的类型必须是INNODB才支持事务**



##### <font color="green">基于JDBC设计DAO实例</font>

DAO：（数据访问对象）<font color="greenyellow"><u>把数据库相关的操作都封装在这个类里面，其他地方看不到JDBC的代码</u></font>

###### DAO接口

```java
public interface DAO{
    //增加
    public void add(Hero hero);
    //修改
    public void update(Hero hero);
    //删除
    public void delete(int id);
    //获取
    public Hero get(int id);
    //查询
    public List<Hero> list();
    //分页查询
    public List<Hero> list(int start, int count);
}
```

###### 设计类HeroDAO，实现接口DAO

```java
public class HeroDAO implements DAO{
    //初始化数据库驱动			代码变得更容易维护，而且也更加简洁
    public HeroDAO() {
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
  	//数据库连接
    public Connection getConnection() throws SQLException {
        return DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8", "root",
                "admin");
    }
  
    public int getTotal() {
        int total = 0;
        try (Connection c = getConnection(); Statement s = c.createStatement();) {
  
            String sql = "select count(*) from hero";
  
            ResultSet rs = s.executeQuery(sql);
            while (rs.next()) {
                total = rs.getInt(1);
            }
  
            System.out.println("total:" + total);
  
        } catch (SQLException e) {
  
            e.printStackTrace();
        }
        return total;
    }
  
    public void add(Hero hero) {
  
        String sql = "insert into hero values(null,?,?,?)";
        try (Connection c = getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {
  
            ps.setString(1, hero.name);
            ps.setFloat(2, hero.hp);
            ps.setInt(3, hero.damage);
  
            ps.execute();
  
            ResultSet rs = ps.getGeneratedKeys();
            if (rs.next()) {
                int id = rs.getInt(1);
                hero.id = id;
            }
        } catch (SQLException e) {
  
            e.printStackTrace();
        }
    }
  
    public void update(Hero hero) {
  
        String sql = "update hero set name= ?, hp = ? , damage = ? where id = ?";
        try (Connection c = getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {
  
            ps.setString(1, hero.name);
            ps.setFloat(2, hero.hp);
            ps.setInt(3, hero.damage);
            ps.setInt(4, hero.id);
  
            ps.execute();
  
        } catch (SQLException e) {
  
            e.printStackTrace();
        }
  
    }
  
    public void delete(int id) {
  
        try (Connection c = getConnection(); Statement s = c.createStatement();) {
  
            String sql = "delete from hero where id = " + id;
  
            s.execute(sql);
  
        } catch (SQLException e) {
  
            e.printStackTrace();
        }
    }
  
    public Hero get(int id) {
        Hero hero = null;
  
        try (Connection c = getConnection(); Statement s = c.createStatement();) {
  
            String sql = "select * from hero where id = " + id;
  
            ResultSet rs = s.executeQuery(sql);
  
            if (rs.next()) {
                hero = new Hero();
                String name = rs.getString(2);
                float hp = rs.getFloat("hp");
                int damage = rs.getInt(4);
                hero.name = name;
                hero.hp = hp;
                hero.damage = damage;
                hero.id = id;
            }
  
        } catch (SQLException e) {
  
            e.printStackTrace();
        }
        return hero;
    }
  	//分页
    public List<Hero> list() {
        return list(0, Short.MAX_VALUE);
    }
  	
    public List<Hero> list(int start, int count) {
        List<Hero> heros = new ArrayList<Hero>();
  
        String sql = "select * from hero order by id desc limit ?,? ";
  
        try (Connection c = getConnection(); PreparedStatement ps = c.prepareStatement(sql);) {
  
            ps.setInt(1, start);
            ps.setInt(2, count);
  
            ResultSet rs = ps.executeQuery();
  
            while (rs.next()) {
                Hero hero = new Hero();
                int id = rs.getInt(1);
                String name = rs.getString(2);
                float hp = rs.getFloat("hp");
                int damage = rs.getInt(4);
                hero.id = id;
                hero.name = name;
                hero.hp = hp;
                hero.damage = damage;
                heros.add(hero);
            }
        } catch (SQLException e) {
  
            e.printStackTrace();
        }
        return heros;
    }
  
}
```

#### <font color="yellow">数据库连接池</font>

传统方式：

当有多个线程，每个线程都需要连接数据库执行SQL语句的话，那么每个线程都会创建一个连接，并且在使用完毕后，关闭连接。

创建连接和关闭连接的过程也是比较消耗时间的，当多线程并发的时候，系统就会变得很卡顿。

同时，一个数据库同时支持的连接总数也是有限的，如果多线程并发量很大，那么数据库连接的总数就会被消耗光，后续线程发起的数据库连接就会失败

<font color="#de5620">含义：</font>

如果有任何线程需要使用连接，那么就从连接池里面**借用**，**而不是自己重新创建**.
使用完毕后，又把这个连接**归还**给连接池供下一次或者其他线程使用。
倘若发生多线程并发情况，连接池里的连接被**借用光**了，那么其他线程就会临时等待，直到有连接被**归还**回来，再继续使用。
整个过程，这些连接都**不会被关闭**，而是不断的被循环使用，从而节约了启动和关闭连接的时间。

```java
public class ConnectionPool {
  
    List<Connection> cs = new ArrayList<Connection>();
  
    int size;
  	//约定了这个连接池一共有多少连接
    public ConnectionPool(int size) {
        this.size = size;
        init();
    }
  	//创建了size条连接。
    public void init() {
          
        //这里恰恰不能使用try-with-resource的方式，因为这些连接都需要是"活"的，不要被自动关闭了
        try {
            Class.forName("com.mysql.jdbc.Driver");
            for (int i = 0; i < size; i++) {
                Connection c = DriverManager
                        .getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8", "root", "admin");
  
                cs.add(c);
  
            }
        } catch (ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
  	//判断是否为空，如果是空的就wait等待，否则就借用一条连接出去
    public synchronized Connection getConnection() {
        while (cs.isEmpty()) {
            try {
                this.wait();
            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
        Connection c = cs.remove(0);
        return c;
    }
  	//在使用完毕后，归还这个连接到连接池，并且在归还完毕后，调用notifyAll，通知那些等待的线程，有新的连接可以借用了
    public synchronized void returnConnection(Connection c) {
        cs.add(c);
        this.notifyAll();
    }
  
}
```

#### PreparedStatement详解

```Java
private static void preparedStatement() {
        // 总结一下JDBC的最基本的使用过程
        // 1、加载驱动类：Class.forName()
        // 2、获取数据库连接：DriverManager.getConnection()
        // 3、创建SQL语句执行句柄：Connection.createStatement()
        // 4、执行SQL语句：Statement.executeUpdate()
        // 5、释放数据库连接资源：finally，Connection.close()

        // 定义数据库连接对象
        // 引用JDBC相关的所有接口或者是抽象类的时候，必须是引用java.sql包下的
        // java.sql包下的，才代表了java提供的JDBC接口，只是一套规范
        // 至于具体的实现，则由数据库驱动来提供，切记不要引用诸如com.mysql.jdbc包的类
        Connection conn=null;
    
        //定义SQL语句执行句柄:PrepareStatement对象
        //PreparedStatement对象,其实就是底层会基于Connection数据库连接
        //可以让我们方便的针对数据库中的表,执行增删改查的SQL语句
        //比如和insert update delete和select语句
        PreparedStatement ps=null;
        try {
            // 第一步，加载数据库的驱动，我们都是面向java.sql包下的接口在编程，所以
            // 要想让JDBC代码能够真正操作数据库，那么就必须第一步先加载进来你要操作的数据库的驱动类
            // 使用Class.forName()方式来加载数据库的驱动类
            // Class.forName()是Java提供的一种基于反射的方式，直接根据类的全限定名（包+类）
            // 从类所在的磁盘文件（.class文件）中加载类对应的内容，并创建对应的Class对象
            Class.forName("com.mysql.jdbc.Driver");
            // 获取数据库的连接
            // 使用DriverManager.getConnection()方法获取针对数据库的连接
            // 需要给方法传入三个参数，包括url、user、password
            // 其中url就是有特定格式的数据库连接串，包括“主协议:子协议://主机名:端口号//数据库”
            conn = DriverManager.getConnection(
                    "jdbc:mysql://localhost:3306/spark_project?characterEncoding=utf8",
                    "root",
                    "root"
            );
            // 基于数据库连接Connection对象，创建SQL语句执行句柄，Statement对象
            // prepareStatement对象，就是用来基于底层的Connection代表的数据库连接
            // 允许我们通过java程序，通过prepareStatement对象，向MySQL数据库发送SQL语句
            // 从而实现通过发送的SQL语句来执行增删改查等逻辑
            // 第一个，SQL语句中，值所在的地方，都用问好代表
            String sql = "insert into user(name,age) values(?,?)";
            ps = conn.prepareStatement(sql);
            // 第二个，必须调用PreparedStatement的setX()系列方法，对指定的占位符设置实际的值
            ps.setString(1,"李四");
            ps.setInt(2,26);
            // Statement.executeUpdate()方法，就可以用来执行insert、update、delete语句
            // 返回类型是个int值，也就是SQL语句影响的行数
            // 第三个，执行SQL语句时，直接使用executeUpdate()即可，不用传入任何参数
            int rtn = ps.executeUpdate();

            System.out.println("SQL语句影响了【" + rtn + "】行。");
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            try {
                // 最后一定要记得在finally代码块中，尽快在执行完SQL语句之后，就释放数据库连接
                if (ps != null){
                    ps.close();
                }
                if (conn !=null){
                    conn.close();
                }
            }catch (Exception e){
                e.printStackTrace();
            }
        }
    }
```

