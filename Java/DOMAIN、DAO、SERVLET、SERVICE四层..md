#### DAO

将**对数据库的操作封装**在其内部。

#### Service

封装了业务功能，每个方法对应一个业务功能，例如注册方法、登录方法等。

#### Servlet

用来接收客户端请求，处理与web相关的问题，例如获取客户端的请求参数，然后转发或重定向

#### Domain（相当于entity）

**封装实体类，与数据库对应**



### 数据库连接池

#### JdbcTemplate使用步骤

1、准备DruidDataSource连接池
2、导入依赖的jar包
spring-beans-4.1.2.RELEASE.jar
spring-core-4.1.2.RELEASE.jar
spring-jdbc-4.1.2.RELEASE.jar
spring-tx-4.1.2.RELEASE.jar
com.springsource.org.apache.commons.logging-1.1.1.jar

3、创建JdbcTemplate对象，传入Druid连接池
4、调用execute（可以执行所有SQL语句，一般用于执行DDL语句）、update（用于执行`INSERT`、`UPDATE`、`DELETE`等DML语句）、queryXxx（用于DQL数据查询语句）等方法

```java
JdbcTemplate jdbcTemplate = new JdbcTemplate(DataSourceUtils.getDataSource());//第3步
		jdbcTemplate.execute(sql);//第4步
```

