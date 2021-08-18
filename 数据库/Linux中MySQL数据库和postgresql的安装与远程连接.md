### Linux中MySQL数据库和postgresql的安装与远程连接

#### 1、MySQL安装与配置

```shell
#命令1
sudo apt-get update
#命令2
sudo apt-get install mysql-server
```

##### 检查mysql服务状态

```shell
systemctl status mysql.service
```

#### 配置远程访问

在Ubuntu下MySQL缺省是只允许本地访问的，使用workbench连接工具是连不上的；
如果你要其他机器也能够访问的话，需要进行配置；

1. 在`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`中编辑，在[mysqld]下面加一句`ship-grant-tables`

2. 然后用根用户进入

   ```shell
   sudo mysql -uroot -p	#sudo表示用最高权限进入
   ```

   由于跳过了权限验证，可以不输入密码直接进入；

3. ```shell
   mysql>use mysql;
   mysql>select user,host from users;#查看用户和其对应的端口
   mysql>ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';
   mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '密码' WITH GRANT OPTION; #修改root可连接对象为所有电脑
   mysql>flush privileges;
   mysql>exit;
   ```

4. 然后`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`编辑删除[mysqld]下面一句`ship-grant-tables`

5. 重启MySQL服务

   ```shell
   sudo /etc/init.d/mysql stop
   sudo/etc/init.d/mysql start
   ```

6. 使用navicat测试

##### 卸载

```shell
#首先使用以下命令删除MySQL服务器：
sudo apt-get remove mysql-server
#然后，删除随MySQL服务器自动安装的任何其他软件：
sudo apt-get autoremove
#卸载其他组件：
sudo apt-get remove <<package-name>>
#查看从MySQL APT存储库安装的软件包列表：
dpkg -l | grep mysql | grep ii
```



#### 2、PG安装与配置

```shell
#安装
sudo apt-get update
sudo apt-get install postgresql postgresql-client
```

1、安装完毕后，系统会创建一个数据库超级用户 postgres，密码为空。

```shell
#  sudo -i -u postgres
```

2、这时使用以下命令进入 postgres，输出以下信息，说明安装成功：

```shell
~$ psql
psql (9.5.17)
Type "help" for help.

postgres=# 
```

3、输入以下命令退出 PostgreSQL 提示符：

```shell
\q
```

4、修改postgres用户的密码

```shell
u - postgres
$ psql
# ALTER USER postgres WITH PASSWORD 'postgres';		#出现alter role即可
# select * from pg_shadow ;
```

修改 **postgresql.conf** 文件：将 listen_addresses = ‘localhsot’ 修改为 listen_addresses = ‘*’

![修改前](https://img-blog.csdnimg.cn/20200903095301493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5namlhbjg2NDE=,size_16,color_FFFFFF,t_70#pic_center)

![修改后](https://img-blog.csdnimg.cn/20200903095310990.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5namlhbjg2NDE=,size_16,color_FFFFFF,t_70#pic_center)

修改 **pg_hba.conf** 文件：在如图位置新增一条记录，将要连接的IP添加进来

添加一行命令`host all all 0.0.0.0/0 md5`

`host  all    all    192.168.1.0/24    trust`（表示允许网段192.168.1.0上的所有主机使用所有合法的数据库用户名访问数据库，其中，数字24是子网掩码，表示允许192.168.1.0–192.168.1.255的计算机访问）

重启数据库即可远程连接



---

#### MySQL、sqlite、Postgresql关于sql语句的区别

##### MySQL

###### KEY关键字

单独使用时相当于index索引（与下面sqlite做对比）；

```sql
DROP TABLE IF EXISTS `info_project`;
CREATE TABLE `info_project` (
  `project_id` int NOT NULL AUTO_INCREMENT COMMENT '项目ID\r\n',
  `PKIAA` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '项目统一编号\r\n',
  `project_docname` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '项目报告名称',
  `data_batch` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '资料批次',
  `unit_id` int DEFAULT NULL COMMENT '单位ID',
  `filestr_unitnum` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '档案保管单位编号',
  `project_type` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '项目类型',
  `profess_type` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '专业类别',
  `district` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '所在行政区',
  `city` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '所在城市',
  `project_status` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '项目运行状况',
  `filetransfer_status` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '档案汇交状况',
  `project_principal` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '项目负责人',
  `project_principalphone` varchar(50) DEFAULT NULL COMMENT '项目负责人电话',
  `project_undertaker` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '项目承担单位',
  `project_startime` date DEFAULT NULL COMMENT '项目起始日期',
  `project_endtime` date DEFAULT NULL COMMENT '项目完成日期',
  `regist_person` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '登记人',
  `regist_unit` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '登记单位',
  `regist_date` date DEFAULT NULL COMMENT '登记日期',
  PRIMARY KEY (`project_id`) USING BTREE,
  KEY `unit_id` (`unit_id`) USING BTREE,
  KEY `project_id` (`project_id`,`PKIAA`,`project_docname`(191)) USING BTREE,
  CONSTRAINT `info_project_ibfk_1` FOREIGN KEY (`unit_id`) REFERENCES `info_fmunit` (`unit_id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=3196 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC;
```

###### 视图

```sql
CREATE OR REPLACE drill_info1 (point_id,PKIAA,SCHAMAA,drillregistrat_id,drill_category,drillaudit_id,approval_status) AS SELECT info_point.point_id,info_point.PKIAA,info_point.SCHAMAA,explore_drillregist.drillregistrat_id,explore_drillregist.drill_category,explore_drillaudit.drillaudit_id,explore_drillaudit.approval_status FROM info_point a,explore_drillregist b,explore_drillaudit c WHERE a.point_id=b.point_id,b.drillregistrat_id=c.registrat_id;


CREATE VIEW standard_info (standardpoint_id,point_id,PKIAA,point_number,STKCAF,STKCAG,Splaneco_sys,SGCJCBJ,Selevation_datum,select_status) AS FROM info_standardpoint.standardpoint_id,info_standardpoint.point_id,info_standardpoint.PKIAA,info_standardpoint.point_number,info_standardpoint.STKCAF,info_standardpoint.STKCAG,info_standardpoint.Splaneco_sys,info_standardpoint.SGCJCBJ,info_standardpoint.Selevation_datum,explore_drillaudit.select_status FROM info_standardpoint,explore_drillaudit WHERE info_standardpoint.PKIAA=explore_drillaudit.PKIAA AND info_standardpoint.point_number=explore_drillaudit.drill_number;

```



##### sqlite

```sql
DROP TABLE IF EXISTS `info_project`;
CREATE TABLE `info_project` (
  `project_id` INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
  `PKIAA` varchar(100)  NOT NULL,
  `project_docname` varchar(255)  NOT NULL,
  `data_batch` varchar(50)  DEFAULT NULL,
  `unit_id` int DEFAULT NULL,
  `filestr_unitnum` varchar(50)  NOT NULL,
  `project_type` varchar(50)  DEFAULT NULL,
  `profess_type` varchar(50)  DEFAULT NULL,
  `district` varchar(50)  DEFAULT NULL,
  `city` varchar(50)  DEFAULT NULL,
  `project_status` varchar(50)  DEFAULT NULL,
  `filetransfer_status` varchar(50)  DEFAULT NULL,
  `project_principal` varchar(50)  DEFAULT NULL,
  `project_principalphone` varchar(50) DEFAULT NULL,
  `project_undertaker` varchar(50)  DEFAULT NULL,
  `project_startime` date DEFAULT NULL,
  `project_endtime` date DEFAULT NULL,
  `regist_person` varchar(50)  DEFAULT NULL,
  `regist_unit` varchar(50)  DEFAULT NULL,
  `regist_date` date DEFAULT NULL,
  CONSTRAINT `info_project_ibfk_1` FOREIGN KEY (`unit_id`) REFERENCES `info_fmunit` (`unit_id`) ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE INDEX project_id ON info_project (project_id, PKIAA,project_docname);
CREATE UNIQUE INDEX unit_id ON info_project (unit_id);
```



##### Postgresql