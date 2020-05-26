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
```

###### 插入

```mysql
//插入一条记录盖伦到表中，值为空（自增）、'盖伦'、616、100
insert into hero values (null, '盖伦', 616, 100)
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

```mysql
//将id为1的数据hp修改为818
update hero set hp = 818 where id = 1
```

###### 删除

```mysql
//删除id为1的数据
delete from hero where id = 1
```

###### [MySql完全卸载](https://how2j.cn/k/mysql/mysql-uninstall/1322.html)

