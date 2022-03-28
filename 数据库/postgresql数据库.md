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