句柄：是Windows用来标识被应用程序所创建或使用的对象的整数。
    当一个应用程序要引用其他程序（如数据库/OS）所管理的内存块或对象时使用句柄。
    调用的是句柄封装好的所提供的服务

 Sqlite *pdb 数据库句柄，类似文件句柄File
 Sqlite_stmt *stmt 保存编译好的SQL语句，相当于ODBC的command
 Sqlite_open() 打开数据库 是操作SQLite数据库的入口函数。该函数返回的database_connection对象是很多其他SQLite APIs的句柄参数
 Sqlite_exec() 执行非查询的sql语句
 Sqlite_prepare_v2() 将SQL文本初始化为待执行的状态，执行select/parameter bind时使用(封装了sqlite_exec)
 Sqlite_step() 调用sqlite_prepare后，使用这个函数在记录中移动
 Sqlite_close()关闭数据库

 用于从记录字段中获取不同类型的数据：sqlite_cloumn_text();sqlite_cloumn_blob();sqlite_cloumn_int()
 获取当前行指定列的数据sqlite_cloumn

 sqlite3_finalize该函数用于销毁prepared statement对象，否则将会造成内存泄露
 Field 字段 