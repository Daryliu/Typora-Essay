[TOC]

# 使用JdbcTemplate并配置多数据源

## 引入包

只需要引入*`spring-boot-starter-jdbc`*依赖即可

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

数据库驱动为ojdbc6，数据源采用Druid

## 代码演示

主要演示在Dao的实现类里使用JdbcTemplate，源码参考[SpringAll/04.Spring-Boot-JdbcTemplate at master · wuyouzhuguli/SpringAll (github.com)](https://github.com/wuyouzhuguli/SpringAll/tree/master/04.Spring-Boot-JdbcTemplate)

- StudentDaoImp类

  ```java
  @Repository("studentDao")
  public class StudentDaoImp implements StudentDao {
  
      @Autowired
      private JdbcTemplate jdbcTemplate;
  
      @Override
      public int add(Student student) {
          // String sql = "insert into student(sno,sname,ssex) values(?,?,?)";
          // Object[] args = { student.getSno(), student.getName(), student.getSex() };
          // int[] argTypes = { Types.VARCHAR, Types.VARCHAR, Types.VARCHAR };
          // return this.jdbcTemplate.update(sql, args, argTypes);
          String sql = "insert into student(sno,sname,ssex) values(:sno,:name,:sex)";
          NamedParameterJdbcTemplate npjt = new NamedParameterJdbcTemplate(this.jdbcTemplate.getDataSource());
          return npjt.update(sql, new BeanPropertySqlParameterSource(student));
      }
  
      @Override
      public int update(Student student) {
          String sql = "update student set sname = ?,ssex = ? where sno = ?";
          Object[] args = { student.getName(), student.getSex(), student.getSno() };
          int[] argTypes = { Types.VARCHAR, Types.VARCHAR, Types.VARCHAR };
          return this.jdbcTemplate.update(sql, args, argTypes);
      }
  
      @Override
      public int deleteBysno(String sno) {
          String sql = "delete from student where sno = ?";
          Object[] args = { sno };
          int[] argTypes = { Types.VARCHAR };
          return this.jdbcTemplate.update(sql, args, argTypes);
      }
  
      @Override
      public List<Map<String, Object>> queryStudentsListMap() {
          String sql = "select * from student";
          return this.jdbcTemplate.queryForList(sql);
      }
  
      @Override
      public Student queryStudentBySno(String sno) {
          String sql = "select * from student where sno = ?";
          Object[] args = { sno };
          int[] argTypes = { Types.VARCHAR };
          List<Student> studentList = this.jdbcTemplate.query(sql, args, argTypes, new StudentMapper());
          if (studentList != null && studentList.size() > 0) {
              return studentList.get(0);
          } else {
              return null;
          }
      }
  }
  ```

  在引入*`spring-boot-starter-jdbc`*驱动后，可直接在类中注入JdbcTemplate`；`

  对于保存操作有两种不同的方法，当**插入的表字段较多的情况**下，推荐使用*`NamedParameterJdbcTemplate`*

  对于返回结果，可以直接使用*`List<Map<String, Object>>`*来接收，这也是个人比较推荐使用的方式，毕竟比较简单方便；也可以使用库表对应的实体对象来接收，不过这时候我们就需要手动创建一个实现了*`org.springframework.jdbc.core.RowMapper`*的对象，用于将实体对象属性和库表字段一一对应

  ```java
  public class StudentMapper implements RowMapper<Student>{
      @Override
      public Student mapRow(ResultSet rs, int rowNum) throws SQLException {
          Student student = new Student();
          student.setSno(rs.getString("sno"));
          student.setName(rs.getString("sname"));
          student.setSex(rs.getString("ssex"));
          return student;
      }
  }
  ```

  

  # JdbcTemplate配置Druid多数据源

  JdbcTemplate配置Druid多数据源的核心在于创建JdbcTemplate时候为其分配不同的数据源，然后在需要访问不同数据库的时候使用对应的JdbcTemplate即可。

  1. 引入多数据源的依赖

     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-jdbc</artifactId>
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

  2. 多数据源配置

     1. 接着在Spring Boot配置文件application.yml中配置多数据源

     ```yaml
     server:
       context-path: /web
     
     spring:
       datasource:
         druid:
           # 数据库访问配置, 使用druid数据源
           # 数据源1 mysql
           mysql:
             type: com.alibaba.druid.pool.DruidDataSource
             driver-class-name: com.mysql.jdbc.Driver
             url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&rewriteBatchedStatements=true&autoReconnect=true&failOverReadOnly=false&zeroDateTimeBehavior=convertToNull
             username: root
             password: 123456
           # 数据源2 oracle
           oracle:
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

     b.  然后创建一个多数据源配置类，根据application.yml分别配置一个Mysql和Oracle的数据源，并且将这两个数据源注入到两个不同的JdbcTemplate中：

     ```java
     @Configuration
     public class DataSourceConfig {
     		//表示是主数据源
     		//@Primary标志这个Bean如果在多个同类Bean候选时，该Bean优先被考虑。多数据源配置的时候，必须要有一个主数据源，用@Primary标志该Bean。
         @Primary
         @Bean(name = "mysqldatasource")
         @ConfigurationProperties("spring.datasource.druid.mysql")
         public DataSource dataSourceOne(){
             return DruidDataSourceBuilder.create().build();
         }
     
         @Bean(name = "oracledatasource")
         @ConfigurationProperties("spring.datasource.druid.oracle")
         public DataSource dataSourceTwo(){
     				//创建数据源
             return DruidDataSourceBuilder.create().build();
         }
     
         @Bean(name = "mysqlJdbcTemplate")
         public JdbcTemplate primaryJdbcTemplate(
                 @Qualifier("mysqldatasource") DataSource dataSource) {
     				//将数据源注入到不同的JdbcTemplate
             return new JdbcTemplate(dataSource);
         }
     
         @Bean(name = "oracleJdbcTemplate")
         public JdbcTemplate secondaryJdbcTemplate(
                 @Qualifier("oracledatasource") DataSource dataSource) {
             return new JdbcTemplate(dataSource);
         }
     }
     ```

     c.  接着创建两个Dao及其实现类，分别用于从Mysql和Oracle中获取数据

     1. MysqlStudentDao接口

     ```java
     public interface MysqlStudentDao {
         List<Map<String, Object>> getAllStudents();
     }
     ```

     1. MysqlStudentDao实现

     ```java
     @Repository
     public class MysqlStudentDaoImp implements MysqlStudentDao{
     		//注入的是mysqlJdbcTemplate
         @Autowired
         @Qualifier("mysqlJdbcTemplate")
         private JdbcTemplate jdbcTemplate;
     
         @Override
         public List<Map<String, Object>> getAllStudents() {
             return this.jdbcTemplate.queryForList("select * from student");
         }
     }
     ```

     1. OracleStudentDao接口

     ```java
     public interface OracleStudentDao {
         List<Map<String, Object>> getAllStudents();
     }
     ```

     1. OracleStudentDao实现

     ```java
     @Repository
     public class OracleStudentDaoImp implements OracleStudentDao{
         @Autowired
         @Qualifier("oracleJdbcTemplate")
         private JdbcTemplate jdbcTemplate;
     
         @Override
         public List<Map<String, Object>> getAllStudents() {
             return this.jdbcTemplate.queryForList("select * from student");
         }
     }
     ```

     1. 随后编写Service层

     ```java
     public interface StudentService {
         List<Map<String, Object>> getAllStudentsFromOralce();
         List<Map<String, Object>> getAllStudentsFromMysql();
     }
     ```

     1. 接口实现

     ```java
     @Service("studentService")
     public class StudentServiceImp implements StudentService{
         @Autowired
         private OracleStudentDao oracleStudentDao;
         @Autowired
         private MysqlStudentDao mysqlStudentDao;
     
         @Override
         public List<Map<String, Object>> getAllStudentsFromOralce() {
             return this.oracleStudentDao.getAllStudents();
         }
     
         @Override
         public List<Map<String, Object>> getAllStudentsFromMysql() {
             return this.mysqlStudentDao.getAllStudents();
         }
     }
     ```

     1. controller层

     ```java
     @Service("studentService")
     public class StudentServiceImp implements StudentService{
         @Autowired
         private OracleStudentDao oracleStudentDao;
         @Autowired
         private MysqlStudentDao mysqlStudentDao;
     
         @Override
         public List<Map<String, Object>> getAllStudentsFromOralce() {
             return this.oracleStudentDao.getAllStudents();
         }
     
         @Override
         public List<Map<String, Object>> getAllStudentsFromMysql() {
             return this.mysqlStudentDao.getAllStudents();
         }
     }
     ```

     [源代码](https://github.com/wuyouzhuguli/Spring-Boot-Demos/tree/master/06.Spring-Boot-JdbcTemplate-MultiDataSource)