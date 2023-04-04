[TOC]

🟠🟡🟢🟣🟤🟥🟧🟨🟩🟦🟪🟫♡

### 1、基础命令

###### 数据库内操作

**接入PostgreSQL数据库: psql -h IP地址 -p 端口 -U 数据库名**

1. 列举数据库：\l
2. 选择数据库：\c  数据库名
3. 查看该某个库中的所有表：\dt
4. 切换数据库：\c interface
5. 查看某个库中的某个表结构：\d 表名
6. 查看某个库中某个表的记录：select * from apps limit 1;
7. 显示字符集：\encoding
8. 退出psgl：\q
8. 显示表空间信息：\db+

###### **`EXPLAIN`函数**

给计划树中每个结点都输出一行，显示基本的结点类型和计划器为该计划结点的执行所做的开销估计。 第一行（最上层的结点）是对该计划的总执行开销的估计。

```sql
EXPLAIN SELECT * FROM tenk1;

            QUERY PLAN 
-------------------------------------------------------------  
Seq Scan on tenk1 (cost=0.00..458.00 rows=10000 width=244)
```

因为这个查询没有WHERE子句，所以必须扫描表中的所有行，所以规划器选择使用简单的顺序扫描规划。括号中的数字从左到右依次是：

- 评估开始消耗。这是可以开始输出前的时间，比如排序节点的排序的时间。
- 评估总消耗。假设查询从执行到结束的时间。有时父节点可能停止这个过程，比如LIMIT子句。
- 评估查询节点的输出行数，假设该节点执行结束。
- 评估查询节点的输出行的平均字节数。

这个消耗的计算依赖于规划器的设置参数，这里的例子都是在默认参数下运行。

需要知道的是：上级节点的消耗包括其子节点的消耗。这个消耗值只反映规划器关心的内容，一般这个消耗不包括将数据传输到客户端的时间。

评估的行数不是执行和扫描查询节点的数量，而是节点返回的数量。它通常会少于扫描数量，因为有WHERE条件会过滤掉一些数据。理想情况顶级行数评估近似于实际返回的数量

回到刚才的例子，表tenk1有10000条数据分布在358个磁盘页，评估时间是（磁盘页*seq_page_cost）+（扫描行*cpu_tuple_cost）。默认seq_page_cost是1.0，cpu_tuple_cost是0.01，所以评估值是(358 * 1.0) + (10000 * 0.01) = 458

###### `FETCH`语法

```sql
OFFSET start { ROW | ROWS }	--从第几行开始
FETCH { FIRST | NEXT } [ row_count ] { ROW | ROWS } ONLY  --只输出多少行数据
```

###### `INTERVAL`语法

```sql
//当前时间减去7分钟的时间 select sysdate,sysdate - interval ’7’ MINUTE from dual

//当前时间减去7小时的时间 select sysdate - interval ’7’ hour from dual

//当前时间减去7天的时间 select sysdate - interval ’7’ day from dual

//当前时间减去7月的时间 select sysdate,sysdate - interval ’7’ month from dual

//当前时间减去7年的时间 select sysdate,sysdate - interval ’7’ year from dual

//时间间隔乘以一个数字 select sysdate,sysdate - 8 *interval ’2’ hour from dual
```

###### `date_trunc`语法

```sql
用法：date_trunc('datepart', field)

SELECT DATE_TRUNC('hour', TIMESTAMP '2020-03-17 02:09:30');//截断一个TIMESTAMP小时日期部分（Result：2020-03-17 02：00：00）
SELECT date_trunc('year', TIMESTAMP '2001-02-16 20:38:40');								Result: 2001-01-01 00:00:00
select date_trunc('weak',now())  + interval '1d 1minute';		//每周的周二第一分钟	
```

###### 被占用强制删除

> 设置数据库为禁止连接

```sql
UPDATE pg_database 
SET datallowconn = 'false' 
WHERE datname = 'db_name';
```

> 中断当前库所有连接会话

```sql
SELECT pg_terminate_backend(pid) 
FROM pg_stat_activity 
WHERE datname = 'db_name';
```

> 删除库

```sql
drop database db_name;
```



### 2、安装问题

- 在安装配置TimescaleDB时若出现问题，且启动Pg数据库服务失败，产生的原因是加载时间超时的问题，可能时安装时改变了postgresql.conf文件，只需要将`shared_preload_libraries = 'timescaledb'`注释即可
- 确保已安装完成PG数据库后，在https://docs.timescale.com/install/latest/self-hosted/中选择需要下载的文件下载TimescaleDB，随后按照该https://www.cnblogs.com/88223100/p/how_to_install_timescaledb_on_windows.html博客步骤执行即可

### 3、TimescaleDB时序数据库

`CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;`<font color = 'red'>添加timescaleDB扩展</font>

时序数据库：多用于物联网，例如水文监控、设备监控、数据监控、通讯监控、传感器监控等；<u>解决传统关系型数据库在时序数据存储和分析上的不足和缺陷</u>。

<u>面向的是海量数据的写入存储读取，单机是无法解决问题的。所以需要采用多机存储，也就是分布式存储。</u>

特点：

1. 基本都是插入，没有更新的需求
2. 数据基本都有时间属性，随着时间的推移不断的产生新的数据，旧的数据不需要保存太久，存在明显的冷热数据，一般只会频繁查找近期数据

优点：

1. 存储成本：

   利用时间递增、维度重复、指标平滑变化的特性，合理选择编码压缩算法，提高数据压缩比；通过预降精度，对历史数据做聚合，节省存储空间。

2. 高并发写入：

   批量写入数据，降低网络开销；数据先写入内存，再周期性的dump为不可变的文件存储。

3. 低查询延时，高查询并发：

   优化常见的查询模式，通过索引等技术降低查询延时；
   通过缓存、routing等技术提高查询并发。

##### 时间分片

如下图，第一行和第三行都是同样的 tag（sensor=95D8-7913;city= 上海），所以分配到同样的分片，而第五行虽然也是同样的 tag，但是根据时间范围再分段，被分到了不同的分片。第二、四、六行属于同样的 tag（sensor=F3CC-20F3;city= 北京）也是一样的道理。

![](https://github.com/Daryliu/Typora-Essay/blob/master/image/%E6%97%B6%E9%97%B4%E5%88%86%E7%89%87.png)

**通过时间进行分组排序时，由于 Timescale 的自动时间和空间分区，使用 TimescaleDB 与普通 PostgreSQL 数据库相比，在大型数据集上的执行速度提高了 20 倍。**

#### 3.1 超表和块

超表是与数据交互的主要点，提供了可以通过标准SQL查询的标准表抽象。

几乎所有与 TimescaleDB 的用户交互都使用超表。插入、更新或删除数据、通过 SELECT 查询数据、更改表、添加新列或索引、与其他表或超表连接等等都可以（并且应该）在超表上执行。

🧡超表实际上是许多实际存储数据的单个表的抽象或虚拟视图，称为**块**。

SQL操作详情可参考https://docs.timescale.com/api/latest/hypertable/create_hypertable/#create-hypertable

##### 3.1.1 创建超表⭐⭐

```sql
--创建常规表
CREATE TABLE conditions (
time TIMESTAMPTZ not null,
location text not null,
temperature DOUBLE PRECISION null,
humidity DOUBLE PRECISION null
);

#将表conditions转换为超表，仅在列上进行时间分区time：
SELECT create_hypertable('conditions', 'time');

#将表conditions转换为超表，设置chunk_time_interval为 24 小时。==>意思是按照24小时划分数据块
SELECT create_hypertable('conditions', 'time', chunk_time_interval => 86400000000);
SELECT create_hypertable('conditions', 'time', chunk_time_interval => INTERVAL '1 day');

#将表conditions转换为具有时间分区time和空间分区location（4 个分区）的超表：
SELECT create_hypertable('conditions', 'time', 'location', 4);

#与上面相同，但使用自定义分区函数：
SELECT create_hypertable('conditions', 'time', 'location', 4, partitioning_func => 'location_hash');

#将表conditions转换为超表。conditions如果已经是超表，则不要发出警告：
SELECT create_hypertable('conditions', 'time', if_not_exists => TRUE);

#重新设置空间分区，只会对新创建的分区生效
SELECT set_number_partitions('conditions', 2);#单个维度
SELECT set_number_partitions('conditions', 2, 'device_id'); #多个维度
```

⭐⭐**时间间隔：**⭐⭐TimescaleDB 的支持手动和自动调整其时间间隔。使用手动设置的时间间隔，用户应该`chunk_time_interval` 在创建他们的超表时指定一个（默认值为 1 周）。用于新块的间隔可以通过调用来改变[`set_chunk_time_interval()`](https://docs.timescale.com/api/latest/hypertable/set_chunk_time_interval/)。<u>设置间隔使这些块不超过主内存的25%。</u>

⭐⭐**空间分区：**⭐⭐在大多数情况下，建议用户不要使用空间分区。但是，如果创建分布式超表，则创建空间分区很重要，请参阅 [create_distributed_hypertable](https://docs.timescale.com/api/latest/distributed-hypertables/create_distributed_hypertable/)。[add_dimension](https://docs.timescale.com/api/latest/hypertable/add_dimension/)部分描述了空间分区可能对非分布式超表有用的罕见情况。

##### 3.1.2 create index

对创建索引的每个块使用单独事务的能力，而不是对整个超表使用单个事务。这允许`INSERT`在命令的大部分持续时间内同时执行 s 和其他操作`CREATE INDEX`。在单个块上创建索引时，它的功能就像`CREATE INDEX`在该块上调用常规一样，但是其他块完全未阻塞。

```sql
#创建索引（索引可以针对块进行事物，而不是整个hypertable）
#匿名索引
CREATE INDEX ON conditions(time, device_id) WITH (timescaledb.transaction_per_chunk);

#其他索引方法
CREATE INDEX ON conditions(time, location) USING brin
  WITH (timescaledb.transaction_per_chunk);
```

##### 3.1.3 插入数据⭐

```sql
#插入数据
INSERT INTO conditions(time,location,temperature,humidity) VALUES(now(),'office',70.0,50.0);

SELECT * FROM conditions ORDER BY time DESC LIMIT 100;
```

##### 3.1.4 drop_chunks

`drop_chunks();`**批量删除**（删除时间范围完全落在指定时间之前（或之后）的数据块，（不同与delete，直接删除磁盘文件也会清除））

```sql
#批量删除
#older_than 截止点的规范，所有早于此时间戳的完整块都应删除。
#table_name 从中删除块的hypertable或连续聚合。
#newer_than 截止点的规范，所有比此时间戳新的完整块都应删除。
#可选参数
#cascade 数据级联，默认为FALSE

#保留最近三个月以内的数据
SELECT drop_chunks(INTERVAL '3 months', 'tablename');

#删除早于三个月以前的所有数据，包括相关对象（例如，视图）：
SELECT drop_chunks(INTERVAL '3 months', 'tablename', cascade => TRUE);

#删除，前四个月到前三个月之间的数据
SELECT drop_chunks(older_than => INTERVAL '3 months', newer_than => INTERVAL '4 months', table_name => 'tablename')

#删除超过未来三个月的数据
SELECT drop_chunks(newer_than => now() + INTERVAL '3 months', table_name => 'tablename');

#删除2017年之前所有的数据
SELECT drop_chunks('tablename','2017-01-01'::date);
SELECT drop_chunks('tablename',1483228800000); # 时间戳操作也可以
```

##### 3.1.5 show_chunks

`show_chunks`获取与超表关联的块列表

```sql
#获取与表关联的所有块的列表：
SELECT show_chunks('conditions');

#从conditions超过 3 个月的超表中获取所有块：
SELECT show_chunks('conditions', older_than => INTERVAL '3 months');

#从 2017 年之前的超表中获取所有块conditions：
SELECT show_chunks('conditions', older_than => DATE '2017-01-01');
```

##### 3.1.6 reorder_chunk

重新排序单个块的堆以遵循索引的顺序。

```sql
#使用conditions_device_id_time_idx索引对_timescaledb_internal._hyper_1_10_chunk块运行重新排序。
SELECT reorder_chunk('_timescaledb_internal._hyper_1_10_chunk', 'conditions_device_id_time_idx');
```

##### 3.1.7 move_chunk

将数据和索引移动到不同的表空间。

```sql
SELECT move_chunk(
  chunk => '_timescaledb_internal._hyper_1_4_chunk',
  destination_tablespace => 'tablespace_2',		#目的地的表空间
  index_destination_tablespace => 'tablespace_3',
  reorder_index => 'conditions_device_id_time_idx',
  verbose => TRUE
);
```

##### 3.1.8 add_reorder_policy

创建一个策略以在后台对给定超表索引上的块重新排序。

```sql
#创建一个策略以按现有(device_id, time)索引对已完成的块进行重新排序。（见reorder_chunk）。
SELECT add_reorder_policy('conditions', 'conditions_device_id_time_idx');
```

##### 3.1.9 remove_reorder_policy

删除策略以重新排序特定的超表。

```sql
#conditions如果存在，则删除表的现有策略重新排序。
SELECT remove_reorder_policy('conditions', if_exists => true);
```

##### 3.1.10 attach_tablespace

###### 3.1.10.1 表空间

**表空间即PostgreSQL存储数据文件的位置**，其中包括数据库对象。如，索引、表等。
 PostgreSQL使用表空间映射逻辑名称和磁盘物理位置。默认提供了两个表空间：

>  pg_default 表空间存储用户数据，用于共享系统目录
>  pg_global 表空间存储全局数据

###### 3.1.10.2 创建表空间

```sql
#表空间名称不能以pg开头，因为这些名称为系统表空间保留。默认执行CREATE TABLESPACE语句的用户是表空间的拥有者。如果需要给其他用户赋权，可以值后面指定owner关键词。directory_path是表空间使用空目录的绝对路径，PostgreSQL的用户必须拥有该目录的权限可以进行读写操作。一旦创建好表空间，可以在CREATE DATABASE, CREATE TABLE 和 CREATE INDEX 语句中使用。
CREATE TABLESPACE tablespace_name OWNER user_name LOCATION directory_path;
#例子：
postgres=# create tablespace "sensor_tablespace" location '/data/postgresData';

#删除表空间
drop tablespace if exists 表空间名
```

将表空间附加到超表并使用它来存储块。[表空间](https://www.postgresql.org/docs/current/manage-ag-tablespaces.html)是文件系统上的一个 目录，它允许控制单个表和索引在文件系统上的存储位置。一个常见的用例是为特定的存储磁盘创建一个表空间，允许将表存储在那里。🌙

```sql
#将表空间disk1附加到超表conditions：
SELECT attach_tablespace('disk1', 'conditions');
SELECT attach_tablespace('disk2', 'conditions', if_not_attached => true);
```

##### 3.1.11 detach_tablespace

从一个或多个超表中分离表空间。这仅意味着新块不会放置在分离的表空间上。

```sql
#从超表conditions中分离表空间disk1：
SELECT detach_tablespace('disk1', 'conditions');
SELECT detach_tablespace('disk2', 'conditions', if_attached => true);

#从当前用户有权访问的所有超表中分离表空间disk1：
SELECT detach_tablespace('disk1');
```

##### 3.1.12 detach_tablespaces

从超表中分离所有表空间。在超表上发出此命令后，它不再有任何附加的表空间。而是将新块放置在数据库的默认表空间中。

```sql
#从超表conditions中分离所有表空间
SELECT detach_tablespaces('conditions');
```

##### 3.1.13 show_tablespaces

显示附加到超表的表空间。

```sql
SELECT * FROM show_tablespaces('conditions');
#结果如下
 show_tablespaces
------------------
 disk1
 disk2
```

##### 3.1.14 set_chunk_time_interval

在超表上设置`chunk_time_interval`。创建新块时使用新间隔，并且现有块上的时间间隔不会更改。

```sql
#对于 TIMESTAMP 列，设置chunk_time_interval为 24 小时：
SELECT set_chunk_time_interval('conditions', INTERVAL '24 hours');
SELECT set_chunk_time_interval('conditions', 86400000000);

#对于表示为自 UNIX 纪元以来的毫秒数的时间列，设置chunk_time_interval为 24 小时：
SELECT set_chunk_time_interval('conditions', 86400000);
```

##### 3.1.15 set_integer_now_func❓

此函数仅与具有整数（与 TIMESTAMP/TIMESTAMPTZ/DATE 相对）时间值的超表相关。对于这样的超表，它设置了一个以时间列为`now()`单位返回值（当前时间）的函数。这对于在基于整数的表上运行某些策略是必要的。特别是，许多策略仅适用于特定年龄的块，并且需要返回当前时间的函数来确定块的年龄。

```sql
#为具有 unix 时间（自 unix 纪元以来的秒数，UTC）的时间列的超表设置整数 now 函数。
CREATE OR REPLACE FUNCTION unix_now() returns BIGINT LANGUAGE SQL STABLE as $$ SELECT extract(epoch from now())::BIGINT $$;
SELECT set_integer_now_func('test_table_bigint', 'unix_now');
```

##### 3.1.16 add_dimension

向 TimescaleDB 超表添加额外的分区维度。选择作为维度的列可以使用间隔分区（例如，用于第二次分区）或散列分区。

```sql
#首先将表转换为超表conditions，仅在time列上进行时间分区，然后添加一个额外的分区键：共四个分区location
SELECT create_hypertable('conditions', 'time');
SELECT add_dimension('conditions', 'location', number_partitions => 4);

#将表转换为具有时间分区time和空间分区location（2 个分区）的超表conditions，然后添加两个额外的维度。
SELECT create_hypertable('conditions', 'time', 'location', 2);
SELECT add_dimension('conditions', 'time_received', chunk_time_interval => INTERVAL '1 day');
SELECT add_dimension('conditions', 'device_id', number_partitions => 2);
SELECT add_dimension('conditions', 'device_id', number_partitions => 2, if_not_exists => true);

#现在在具有一个访问节点和两个数据节点的集群的分布式超表的多节点示例中，配置访问节点以访问两个数据节点。然后，将表转换为仅在列上进行时间分区time的分布式超表conditions，最后在具有两个分区的location空间分区维度上添加（作为附加数据节点的数量）。
SELECT add_data_node('dn1', host => 'dn1.example.com');
SELECT add_data_node('dn2', host => 'dn2.example.com');
SELECT create_distributed_hypertable('conditions', 'time');
SELECT add_dimension('conditions', 'location', number_partitions => 2);
```

##### 3.1.17 hypertable_size

获取超表使用的总磁盘空间，即表本身（包括块）、表上的任何索引和任何 toast 表的大小之和。大小以字节为单位报告。这相当于从函数`total_bytes`的输出中计算列的总和。

```sql
#获取超表的大小信息。
SELECT hypertable_size('devices') ;
#结果
 hypertable_size
-----------------
           73728

#获取所有超表的大小信息。
SELECT hypertable_name, hypertable_size(format('%I.%I', hypertable_schema, hypertable_name)::regclass)
  FROM timescaledb_information.hypertables;
```

##### 3.1.18 hypertable_detailed_size

获取有关超表使用的磁盘空间的详细信息，返回表本身的大小信息、表上的任何索引、任何 toast 表以及所有表的总大小。所有大小都以字节为单位报告。如果该函数在分布式超表上执行，它会将大小信息作为每个节点（包括访问节点）的单独行返回。

```sql
#获取超表的大小信息。
-- disttable is a distributed hypertable --
SELECT * FROM hypertable_detailed_size('disttable') ORDER BY node_name;

 table_bytes | index_bytes | toast_bytes | total_bytes |  node_name
-------------+-------------+-------------+-------------+-------------
       16384 |       40960 |           0 |       57344 | data_node_1
        8192 |       24576 |           0 |       32768 | data_node_2
           0 |        8192 |           0 |        8192 |
#列出的访问节点没有用户给定的节点名称。正常情况下，访问节点不保存数据，但仍维护例如占用磁盘空间较小的索引信息。
```

##### 3.1.19 hypertable_index_size

获取超表上索引使用的磁盘空间，包括为所有块提供索引所需的磁盘空间。大小以字节为单位报告。

```sql
\d conditions_table
                     Table "public.conditions_table"
 Column |           Type           | Collation | Nullable | Default 
--------+--------------------------+-----------+----------+---------
 time   | timestamp with time zone |           | not null | 
 device | integer                  |           |          | 
 volume | integer                  |           |          | 
Indexes:
    "second_index" btree ("time")
    "test_table_time_idx" btree ("time" DESC)
    "third_index" btree ("time")

SELECT hypertable_index_size('second_index');
#结果
 hypertable_index_size 
-----------------------
                163840

SELECT pg_size_pretty(hypertable_index_size('second_index'));
#结果
 pg_size_pretty 
----------------
 160 kB
```

##### 3.1.20 chunks_detailed_size

获取有关属于超表的块使用的磁盘空间的信息，返回每个块表的大小信息、块上的任何索引、任何 toast 表以及与块关联的总大小。所有大小都以字节为单位报告。

```sql
SELECT * FROM chunks_detailed_size('dist_table')
  ORDER BY chunk_name, node_name;
#结果：
     chunk_schema      |      chunk_name       | table_bytes | index_bytes | toast_bytes | total_bytes |       node_name
-----------------------+-----------------------+-------------+-------------+-------------+-------------+-----------------------
 _timescaledb_internal | _dist_hyper_1_1_chunk |        8192 |       32768 |           0 |       40960 | data_node_1
 _timescaledb_internal | _dist_hyper_1_2_chunk |        8192 |       32768 |           0 |       40960 | data_node_2
 _timescaledb_internal | _dist_hyper_1_3_chunk |        8192 |       32768 |           0 |       40960 | data_node_3
```

#### 3.2 压缩

