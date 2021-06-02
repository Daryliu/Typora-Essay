### MySql

###### 创建表

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

###### 插入

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

###### 查询

```mysql
//查询hero表中所有数据
select * from hero

//统计表中有多少条数据顶
select count(*) from hero

//分页查询  	显示前5条数据    limit arg1,arg2|| arg1指定查询记录的起始位置；arg2用于指定查询数据所返回的记录数
select * from hero limit 0,5
```

###### 修改

> UPDATE 表名 SET column_name=value [,column_name2=value2,...] [WHERE condition];

- column_name 为要更改的数据列
- value 为修改后的数据 , 可以为变量 , 具体指 , 表达式或者嵌套的SELECT结果
- condition 为筛选条件 , 如不指定则修改该表的所有列数据

```mysql
//将id为1的数据hp修改为818
update hero set hp = 818 where id = 1
```

###### 删除

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

###### [MySql完全卸载](https://how2j.cn/k/mysql/mysql-uninstall/1322.html)

