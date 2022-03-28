### 1、基础命令

**接入PostgreSQL数据库: psql -h IP地址 -p 端口 -U 数据库名**

1. 列举数据库：\l
2. 选择数据库：\c  数据库名
3. 查看该某个库中的所有表：\dt
4. 切换数据库：\c interface
5. 查看某个库中的某个表结构：\d 表名
6. 查看某个库中某个表的记录：select * from apps limit 1;
7. 显示字符集：\encoding
8. 退出psgl：\q

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



#### 3.1超表

```sql
--创建常规表
CREATE TABLE conditions (
time TIMESTAMPTZ not null,
location text not null,
temperature DOUBLE PRECISION null,
humidity DOUBLE PRECISION null
);

--转换成超表
SELECT create_hypertable('conditions','time');

INSERT INTO conditions(time,location,temperature,humidity) VALUES(now(),'office',70.0,50.0);

SELECT * FROM conditions ORDER BY time DESC LIMIT 100;
```

