### MySql

##### 创建表

```mysql
//创建名为hero的表，包含字段id、name、hp、damage四列，主键id
CREATE TABLE hero (
  id int(11) AUTO_INCREMENT,//自增的id
  name varchar(30) ,
  hp float ,
  damage int(11) ,
  PRIMARY KEY (id)
)  DEFAULT CHARSET=utf8;


-- 创建表时添加外键
-- 创建外键的方式一 : 创建子表同时创建外键
 
-- 年级表 (id\年级名称)
CREATE TABLE `grade` (
  `gradeid` INT(10) NOT NULL AUTO_INCREMENT COMMENT '年级ID',
  `gradename` VARCHAR(50) NOT NULL COMMENT '年级名称',
  PRIMARY KEY (`gradeid`)
) ENGINE=INNODB DEFAULT CHARSET=utf8
 
-- 学生信息表 (学号,姓名,性别,年级,手机,地址,出生日期,邮箱,身份证号)
CREATE TABLE `student` (
  `studentno` INT(4) NOT NULL COMMENT '学号',
  `studentname` VARCHAR(20) NOT NULL DEFAULT '匿名' COMMENT '姓名',
  `sex` TINYINT(1) DEFAULT '1' COMMENT '性别',
  `gradeid` INT(10) DEFAULT NULL COMMENT '年级',
  `phoneNum` VARCHAR(50) NOT NULL COMMENT '手机',
  `address` VARCHAR(255) DEFAULT NULL COMMENT '地址',
  `borndate` DATETIME DEFAULT NULL COMMENT '生日',
  `email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
  `idCard` VARCHAR(18) DEFAULT NULL COMMENT '身份证号',
  PRIMARY KEY (`studentno`),
  KEY `FK_gradeid` (`gradeid`),
  CONSTRAINT `FK_gradeid` FOREIGN KEY (`gradeid`) REFERENCES `grade` (`gradeid`)
) ENGINE=INNODB DEFAULT CHARSET=utf8



-- 删除外键
ALTER TABLE student DROP FOREIGN KEY FK_gradeid;
-- 发现执行完上面的,索引还在,所以还要删除索引
-- 注:这个索引是建立外键的时候默认生成的
ALTER TABLE student DROP INDEX FK_gradeid;
```

##### 插入

```mysql
//插入一条记录盖伦到表中，值为空（自增）、'盖伦'、616、100
insert into hero values (null, '盖伦', 616, 100)

-- 使用语句如何增加语句?
-- 语法 : INSERT INTO 表名[(字段1,字段2,字段3,...)] VALUES('值1','值2','值3')
INSERT INTO grade(gradename) VALUES ('大一');
 
-- 主键自增,那能否省略呢?
INSERT INTO grade VALUES ('大二');
 
-- 查询:INSERT INTO grade VALUE ('大二')错误代码：1136
Column count doesn`t match value count at row 1
 
-- 结论:'字段1,字段2...'该部分可省略 , 但添加的值务必与表结构,数据列,顺序相对应,且数量一致.
 
-- 一次插入多条数据
INSERT INTO grade(gradename) VALUES ('大三'),('大四');
```

##### 查询

```mysql
//查询hero表中所有数据
select * from hero

//统计表中有多少条数据顶
select count(*) from hero

//分页查询  	显示前5条数据    limit arg1,arg2|| arg1指定查询记录的起始位置；arg2用于指定查询数据所返回的记录数
select * from hero limit 0,5
```

##### 修改

> UPDATE 表名 SET column_name=value [,column_name2=value2,...] [WHERE condition];

- column_name 为要更改的数据列
- value 为修改后的数据 , 可以为变量 , 具体指 , 表达式或者嵌套的SELECT结果
- condition 为筛选条件 , 如不指定则修改该表的所有列数据

```mysql
//将id为1的数据hp修改为818
update hero set hp = 818 where id = 1
```

##### 删除

> DELETE FROM 表名 [WHERE condition];



```mysql
//删除id为1的数据
delete from hero where id = 1

-- 创建一个测试表
CREATE TABLE `test` (
  `id` INT(4) NOT NULL AUTO_INCREMENT,
  `coll` VARCHAR(20) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8
 
-- 插入几个测试数据
INSERT INTO test(coll) VALUES('row1'),('row2'),('row3');
 
-- 删除表数据(不带where条件的delete)
DELETE FROM test;
-- 结论:如不指定Where则删除该表的所有列数据,自增当前值依然从原来基础上进行,会记录日志.
 
-- 删除表数据(truncate)
TRUNCATE TABLE test;
-- 结论:truncate删除数据,自增当前值会恢复到初始值重新开始;不会记录日志.
 
-- 同样使用DELETE清空不同引擎的数据库表数据.重启数据库服务后
-- InnoDB : 自增列从初始值重新开始 (因为是存储在内存中,断电即失)
-- MyISAM : 自增列依然从上一个自增数据基础上开始 (存在文件中,不会丢失)
```

##### 事务

- 事务就是将一组SQL语句放在同一批次内去执行
- 若其中一条SQL出错，则所有SQL都不执行
- MYSQL只支持InnoDB和BDB数据表类型

###### 事务原则

1. 原子性

   整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（ROLLBACK）到事务开始前的状态，就像这个事务从来没有执行过一样。

2. 一致性

   一个事务可以封装状态改变（除非它是一个只读的）。事务必须始终保持系统处于一致的状态，不管在任何给定的时间并发事务有多少。也就是说：如果事务是并发多个，系统也必须如同串行事务一样操作。其主要特征是保护性和不变性(Preserving an Invariant)，以转账案例为例，假设有五个账户，每个账户余额是100元，那么五个账户总额是500元，如果在这个5个账户之间同时发生多个转账，无论并发多少个，比如在A与B账户之间转账5元，在C与D账户之间转账10元，在B与E之间转账15元，五个账户总额也应该还是500元，这就是保护性和不变性。

3. 隔离性

   隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作。如果有两个事务，运行在相同的时间内，执行相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统。

4. 持久性

   在事务完成以后，该事务对数据库所作的更改便持久的保存在数据库之中，并不会被回滚

###### 基本语法

```sql
-- 使用set语句来改变自动提交模式
SET autocommit = 0;   /*关闭*/
SET autocommit = 1;   /*开启*/
 
-- 注意:
---  1.MySQL中默认是自动提交
---  2.使用事务时应先关闭自动提交
 
-- 开始一个事务,标记事务的起始点
START TRANSACTION  
 
-- 提交一个事务给数据库
COMMIT
 
-- 将事务回滚,数据回到本次事务的初始状态
ROLLBACK
 
-- 还原MySQL数据库的自动提交
SET autocommit =1;
 
-- 保存点
SAVEPOINT 保存点名称 -- 设置一个事务保存点
ROLLBACK TO SAVEPOINT 保存点名称 -- 回滚到保存点
RELEASE SAVEPOINT 保存点名称 -- 删除保存点
```

###### 事务测试

```sql
/*
课堂测试题目
A在线买一款价格为500元商品,网上银行转账.
A的银行卡余额为2000,然后给商家B支付500.
商家B一开始的银行卡余额为10000
创建数据库shop和创建表account并插入2条数据
*/
 
CREATE DATABASE `shop`CHARACTER SET utf8 COLLATE utf8_general_ci;
USE `shop`;
 
CREATE TABLE `account` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(32) NOT NULL,
  `cash` DECIMAL(9,2) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8
 
INSERT INTO account (`name`,`cash`)
VALUES('A',2000.00),('B',10000.00)
 
-- 转账实现
SET autocommit = 0; -- 关闭自动提交
START TRANSACTION;  -- 开始一个事务,标记事务的起始点
UPDATE account SET cash=cash-500 WHERE `name`='A';
UPDATE account SET cash=cash+500 WHERE `name`='B';
COMMIT; -- 提交事务
# rollback;
SET autocommit = 1; -- 恢复自动提交
```

##### 索引

- 提高查询速度
- 确保数据的唯一性
- 可以加速表和表之间的连接 , 实现表与表之间的参照完整性
- 使用分组和排序子句进行数据检索时 , 可以显著减少分组和排序的时间
- 全文检索字段进行搜索优化

###### 分类

1. 主键索引

   某一个属性组能唯一标识一条记录

2. 唯一索引

   避免同一个表中某数据列中的值重复

   ```sql
   CREATE TABLE `Grade`(
       `GradeID` INT(11) AUTO_INCREMENT PRIMARYKEY,
       `GradeName` VARCHAR(32) NOT NULL UNIQUE
       -- 或 UNIQUE KEY `GradeID` (`GradeID`)
   )
   ```

3. 常规索引

   快速定位特定数据

   ```sql
   CREATE TABLE `result`(
       -- 省略一些代码
       INDEX/KEY `ind` (`studentNo`,`subjectNo`) -- 创建表时添加
   )
   
   -- 创建后添加
   ALTER TABLE `result` ADD INDEX `ind`(`studentNo`,`subjectNo`);
   ```

4. 全文索引

   - 只能用于MyISAM类型的数据表
   - 只能用于CHAR , VARCHAR , TEXT数据列类型
   - 适合大型数据集

   ```sql
   /*
   #方法一：创建表时
       　　CREATE TABLE 表名 (
                   字段名1  数据类型 [完整性约束条件…],
                   字段名2  数据类型 [完整性约束条件…],
                   [UNIQUE | FULLTEXT | SPATIAL ]   INDEX | KEY
                   [索引名]  (字段名[(长度)]  [ASC |DESC])
                   );
   #方法二：CREATE在已存在的表上创建索引
           CREATE  [UNIQUE | FULLTEXT | SPATIAL ]  INDEX  索引名
                        ON 表名 (字段名[(长度)]  [ASC |DESC]) ;
   #方法三：ALTER TABLE在已存在的表上创建索引
           ALTER TABLE 表名 ADD  [UNIQUE | FULLTEXT | SPATIAL ] INDEX
                                索引名 (字段名[(长度)]  [ASC |DESC]) ;
                               
                               
   #删除索引：DROP INDEX 索引名 ON 表名字;
   #删除主键索引: ALTER TABLE 表名 DROP PRIMARY KEY;
   #显示索引信息: SHOW INDEX FROM student;
   */
    
   /*增加全文索引*/
   ALTER TABLE `school`.`student` ADD FULLTEXT INDEX `studentname` (`StudentName`);
    
   /*EXPLAIN : 分析SQL语句执行性能*/
   EXPLAIN SELECT * FROM student WHERE studentno='1000';
    
   /*使用全文索引*/
   -- 全文搜索通过 MATCH() 函数完成。
   -- 搜索字符串作为 against() 的参数被给定。搜索以忽略字母大小写的方式执行。对于表中的每个记录行，MATCH() 返回一个相关性值。即，在搜索字符串与记录行在 MATCH() 列表中指定的列的文本之间的相似性尺度。
   EXPLAIN SELECT *FROM student WHERE MATCH(studentname) AGAINST('love');
    
   /*
   开始之前，先说一下全文索引的版本、存储引擎、数据类型的支持情况
   MySQL 5.6 以前的版本，只有 MyISAM 存储引擎支持全文索引；
   MySQL 5.6 及以后的版本，MyISAM 和 InnoDB 存储引擎均支持全文索引;
   只有字段的数据类型为 char、varchar、text 及其系列才可以建全文索引。
   测试或使用全文索引时，要先看一下自己的 MySQL 版本、存储引擎和数据类型是否支持全文索引。
   */
   ```


##### 数据库连接池

```Java
/**
 * Druid连接池的工具类
 */
public class JDBCUtils {
    //1.定义成员变量 DataSource 连接池对象ds
    private static DataSource ds;
    static {
        try {
            //2.加载配置文件-->配置文件中包括注册驱动等信息
            Properties pro = new Properties();
            InputStream inputStream = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
            pro.load(inputStream);
            //3.获取连接池对象
            ds = DruidDataSourceFactory.createDataSource(pro);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /*
     * 提供获取连接池对象的方法
     * */
    public static DataSource getDataSource(){
        return ds;
    }

    /*
     * 提供获取连接的方法 getConnection
     * */
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }

    /*
     * -->Springtemplate中不需要调用close()
     * 提供2种重载的关闭资源的方法
     * 根据执行DQL,DMl语句来选择
     * */
    public static void close(Statement stmt, Connection conn){
        close(null,stmt,conn);
    }

    public static void close(ResultSet rs, Statement stmt, Connection conn){
        if (rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (stmt != null){
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```



##### [MySql完全卸载](https://how2j.cn/k/mysql/mysql-uninstall/1322.html)

