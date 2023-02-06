[TOC]

# Mybatis集成及配置Druid多数据源

## 1.Pom.xml

引入*`mybatis-spring-boot-starter`*和数据库连接驱动（mybatis默认使用tomcat-jdbc数据源）

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.1</version>
</dependency>
```

不同版本的Spring Boot和MyBatis版本对应不一样，具体可查看官方文档：http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/

## 2.Druid数据源

提供连接池的功能，还提供监控功能，可以实时查看数据库连接池和SQL查询的工作情况。

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>1.1.6</version>
</dependency>
```

Spring Boot的数据源配置的默认类型是*`org.apache.tomcat.jdbc.pool.Datasource`，*要使用Druid数据源必须在application.yml下配置

```yaml
server:
  context-path: /web

spring:
  datasource:
    druid:
      # 数据库访问配置, 使用druid数据源
      type: com.alibaba.druid.pool.DruidDataSource
      driver-class-name: oracle.jdbc.driver.OracleDriver
      url: jdbc:oracle:thin:@localhost:1521:ORCL
      username: scott
      password: 123456
      # 连接池配置
      initial-size: 5
      min-idle: 5
      max-active: 20
      # 连接等待超时时间
      max-wait: 30000
      # 配置检测可以关闭的空闲连接间隔时间
      time-between-eviction-runs-millis: 60000
      # 配置连接在池中的最小生存时间
      min-evictable-idle-time-millis: 300000
      validation-query: select '1' from dual
      test-while-idle: true
      test-on-borrow: false
      test-on-return: false
      # 打开PSCache，并且指定每个连接上PSCache的大小
      pool-prepared-statements: true
      max-open-prepared-statements: 20
      max-pool-prepared-statement-per-connection-size: 20
      # 配置监控统计拦截的filters, 去掉后监控界面sql无法统计, 'wall'用于防火墙
      filters: stat,wall
      # Spring监控AOP切入点，如x.y.z.service.*,配置多个英文逗号分隔
      aop-patterns: com.springboot.servie.*

      # WebStatFilter配置
      web-stat-filter:
        enabled: true
        # 添加过滤规则
        url-pattern: /*
        # 忽略过滤的格式
        exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'

      # StatViewServlet配置
      stat-view-servlet:
        enabled: true
        # 访问路径为/druid时，跳转到StatViewServlet
        url-pattern: /druid/*
        # 是否能够重置数据
        reset-enable: false
        # 需要账号密码才能访问控制台
        login-username: druid
        login-password: druid123
        # IP白名单
        # allow: 127.0.0.1
        #　IP黑名单（共同存在时，deny优先于allow）
        # deny: 192.168.1.218

      # 配置StatFilter
      filter:
        stat:
          log-slow-sql: true
```

## 3.使用mybatis

[*简单的语句只需要使用@Insert、@Update、@Delete、@Select这4个注解即可，动态SQL语句需要使用@InsertProvider、@UpdateProvider、@DeleteProvider、@SelectProvider等注解*](http://www.mybatis.org/mybatis-3/zh/java-api.html)

- 创建库表

  ```sql
  CREATE TABLE "SCOTT"."STUDENT" (
      "SNO" VARCHAR2(3 BYTE) NOT NULL ,
      "SNAME" VARCHAR2(9 BYTE) NOT NULL ,
      "SSEX" CHAR(2 BYTE) NOT NULL
  );
  
  INSERT INTO "SCOTT"."STUDENT" VALUES ('001', 'KangKang', 'M ');
  INSERT INTO "SCOTT"."STUDENT" VALUES ('002', 'Mike', 'M ');
  INSERT INTO "SCOTT"."STUDENT" VALUES ('003', 'Jane', 'F ');
  ```

- 创建对应实体

  ```java
  public class Student implements Serializable{
      private static final long serialVersionUID = -339516038496531943L;
      private String sno;
      private String name;
      private String sex;
      // get,set略
  }
  ```

- 创建一个包含基本CRUD的StudentMapper

  ```java
  public interface StudentMapper {
      int add(Student student);
      int update(Student student);
      int deleteByIds(String sno);
      Student queryStudentById(Long id);
  }
  ```

<aside> 💡 **使用注解方式**

</aside>

- 继续编辑StudentMapper

  ```java
  @Component
  @Mapper
  public interface StudentMapper {
      @Insert("insert into student(sno,sname,ssex) values(#{sno},#{name},#{sex})")
      int add(Student student);
  
      @Update("update student set sname=#{name},ssex=#{sex} where sno=#{sno}")
      int update(Student student);
  
      @Delete("delete from student where sno=#{sno}")
      int deleteBysno(String sno);
  
      @Select("select * from student where sno=#{sno}")
      @Results(id = "student",value= {
  				//对应实体属性property 及数据库中的列名column 
          @Result(property = "sno", column = "sno", javaType = String.class),
          @Result(property = "name", column = "sname", javaType = String.class),
          @Result(property = "sex", column = "ssex", javaType = String.class)
      })
      Student queryStudentBySno(String sno);
  ```

案例二

**一个查询中用到另一个查询的结果**

```java
@Results(id="groupWithUsers",value = {
		@Result(property = "groupId", column = "group_id", id = true),
		@Result(property = "name", column = "name"),
		@Result(property = "accountId", column = "account_id"),
		@Result(property = "deleteFlag", column = "delete_Flag"),
		@Result(property = "parentId", column = "parent_Id"),
		//**一个查询中用到另一个查询的结果    @Many中常用的属性只有select，用于指定关联查询的方法
		/*如果select属性指定的方法需要的参数是一个对象，
		比如下面的方法改为public List<User> selectUsers(Map<String,Object> map);
		则最后改为column = "{groupId=group_id, userName=user_name}"*/**
		@Result(property = "userList", javaType=List.class, many =@Many(select="selectUsersByGroupId"), column = "group_id")})
//查询
@Select({"select * from group where account_id=#{accountId} and delete_flag=0"})
List<Group> selectGroupWithUsers(@Param("accountId") String accountId);

@Select({"select u.* from user u",
"inner join user_group ug on u.user_id = ug.user_id",
"where ug.group_id=#{groupId} and u.delete_flag=0"
})
List<User> selectUsersByGroupId(@Param("groupId") String groupId);
```

<aside> 💡 **使用xml方式**

</aside>

- 使用xml方式需要在application.yml中进行一些额外的配置

```xml
mybatis:
  # type-aliases扫描路径
  # type-aliases-package:
  # mapper xml实现扫描路径
  mapper-locations: classpath:mapper/*.xml
  property:
    order: BEFORE
```

<aside> 💡 测试

</aside>

- Service

  ```java
  public interface StudentService {
      int add(Student student);
      int update(Student student);
      int deleteBysno(String sno);
      Student queryStudentBySno(String sno);
  }
  ```

- 实现类

  ```java
  @Service("studentService")
  public class StudentServiceImp implements StudentService{
      @Autowired
      private StudentMapper studentMapper;
  
      @Override
      public int add(Student student) {
          return this.studentMapper.add(student);
      }
  
      @Override
      public int update(Student student) {
          return this.studentMapper.update(student);
      }
  
      @Override
      public int deleteBysno(String sno) {
          return this.studentMapper.deleteBysno(sno);
      }
  
      @Override
      public Student queryStudentBySno(String sno) {
          return this.studentMapper.queryStudentBySno(sno);
      }
  }
  ```

- controller

  ```java
  @RestController
  public class TestController {
  
      @Autowired
      private StudentService studentService;
  
      @RequestMapping( value = "/querystudent", method = RequestMethod.GET)
      public Student queryStudentBySno(String sno) {
          return this.studentService.queryStudentBySno(sno);
      }
  }
  ```

## 4.MyBatis配置Druid多数据源

实际上在Spring Boot中配置MyBatis多数据源的关键在于创建SqlSessionFactory的时候为其分配不同的数据源。

### 1.引入依赖

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.1</version>
</dependency>

<!-- oracle驱动 -->
<dependency>
    <groupId>com.oracle</groupId>
    <artifactId>ojdbc6</artifactId>
    <version>6.0</version>
</dependency>

<!-- mysql驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>

<!-- druid数据源驱动 -->
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>1.1.6</version>
</dependency>
```

### 2.多数据源配置

application.yml的配置上Jdbc和Mybatis多数据源配置相同。

- 然后根据application.yml创建两个数据源配置类MysqlDatasourceConfig和OracleDatasourceConfig

  ```java
  /*-----------------------------MysqlDatasourceConfig-------------------------------------*/
  @Configuration
  @MapperScan(basePackages = MysqlDatasourceConfig.PACKAGE, sqlSessionFactoryRef = "mysqlSqlSessionFactory")
  public class MysqlDatasourceConfig {
  
      // mysqldao扫描路径
      static final String PACKAGE = "com.springboot.mysqldao";
      // mybatis mapper扫描路径
      static final String MAPPER_LOCATION = "classpath:mapper/mysql/*.xml";
  
      @Primary
      @Bean(name = "mysqldatasource")
      @ConfigurationProperties("spring.datasource.druid.mysql")
      public DataSource mysqlDataSource() {
          return DruidDataSourceBuilder.create().build();
      }
  
      @Bean(name = "mysqlTransactionManager")
      @Primary
      public DataSourceTransactionManager mysqlTransactionManager() {
          return new DataSourceTransactionManager(mysqlDataSource());
      }
  
      @Bean(name = "mysqlSqlSessionFactory")
      @Primary
      public SqlSessionFactory mysqlSqlSessionFactory(@Qualifier("mysqldatasource") DataSource dataSource)
              throws Exception {
          final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
          sessionFactory.setDataSource(dataSource);
          //如果不使用xml的方式配置mapper，则可以省去下面这行mapper location的配置。
          sessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver()
                  .getResources(MysqlDatasourceConfig.MAPPER_LOCATION));
          return sessionFactory.getObject();
      }
  
  		/*@Bean(name = "firstSqlSessionTemplate")
      @Primary
      public SqlSessionTemplate sqlSessionTemplate(@Qualifier("mysqlSqlSessionFactory") SqlSessionFactory sqlSessionFactory) throws Exception {
          return new SqlSessionTemplate(mysqlSqlSessionFactory);
      }*/
  }
  ```

  上面代码配置了一个名为mysqldatasource的数据源，对应application.yml中*`spring.datasource.druid.mysql`*前缀配置的数据库。然后创建了一个名为mysqlSqlSessionFactory的Bean，并且注入了mysqldatasource。与此同时，还分别定了两个扫描路径PACKAGE和MAPPER_LOCATION，前者为Mysql数据库对应的mapper接口地址，后者为对应的mapper xml文件路径。

  *`@Primary`*标志这个Bean如果在多个同类Bean候选时，该Bean优先被考虑。多数据源配置的时候，必须要有一个主数据源，用*`@Primary`*标志该Bean。

  ```java
  /*-----------------------------OracleDatasourceConfig-------------------------------------*/
  @Configuration
  @MapperScan(basePackages = OracleDatasourceConfig.PACKAGE,
      sqlSessionFactoryRef = "oracleSqlSessionFactory")
  public class OracleDatasourceConfig {
  
      // oracledao扫描路径
      static final String PACKAGE = "com.springboot.oracledao";
      // mybatis mapper扫描路径
      static final String MAPPER_LOCATION = "classpath:mapper/oracle/*.xml";
  
      @Bean(name = "oracledatasource")
      @ConfigurationProperties("spring.datasource.druid.oracle")
      public DataSource oracleDataSource() {
          return DruidDataSourceBuilder.create().build();
      }
  
      @Bean(name = "oracleTransactionManager")
      public DataSourceTransactionManager oracleTransactionManager() {
          return new DataSourceTransactionManager(oracleDataSource());
      }
  
      @Bean(name = "oracleSqlSessionFactory")
      public SqlSessionFactory oracleSqlSessionFactory(@Qualifier("oracledatasource") DataSource dataSource)
            throws Exception {
          final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
          sessionFactory.setDataSource(dataSource);
          //如果不使用xml的方式配置mapper，则可以省去下面这行mapper location的配置。
          sessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver()
                  .getResources(OracleDatasourceConfig.MAPPER_LOCATION));
          return sessionFactory.getObject();
      }
  }
  ```

- 测试

  配置完多数据源，接下来分别在com.springboot.mysqldao路径和com.springboot.oracledao路径下创建两个mapper接口。

  ```java
  package com.springboot.mysqldao;
  
  import java.util.List;
  import java.util.Map;
  import org.apache.ibatis.annotations.Mapper;
  
  @Mapper
  public interface MysqlStudentMapper {
      List<Map<String, Object>> getAllStudents();
  }
  ```

  ```java
  package com.springboot.oracledao;
  
  import java.util.List;
  import java.util.Map;
  import org.apache.ibatis.annotations.Mapper;
  
  @Mapper
  public interface OracleStudentMapper {
      List<Map<String, Object>> getAllStudents();
  }
  ```

- 在src/main/resource/mapper/mysql/路径下创建MysqlStudentMapper.xml：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "<http://mybatis.org/dtd/mybatis-3-mapper.dtd>">
  <mapper namespace="com.springboot.mysqldao.MysqlStudentMapper">
      <select id="getAllStudents" resultType="java.util.Map">
          select * from student
      </select>
  </mapper>
  ```

- 在src/main/resource/mapper/oracle/路径下创建OracleStudentMapper.xml：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "<http://mybatis.org/dtd/mybatis-3-mapper.dtd>">
  <mapper namespace="com.springboot.oracledao.OracleStudentMapper">
      <select id="getAllStudents" resultType="java.util.Map">
          select * from student
      </select>
  </mapper>
  ```