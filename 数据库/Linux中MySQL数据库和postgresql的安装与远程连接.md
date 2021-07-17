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

重启数据库即可远程连接