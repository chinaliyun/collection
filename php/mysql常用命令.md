```

使用SHOW语句找出在服务器上当前存在什么数据库：
mysql> SHOW DATABASES;
创建一个数据库MYSQLDATA
mysql> CREATE DATABASE MYSQLDATA;
选择你所创建的数据库
mysql> USE MYSQLDATA; (按回车键出现Database changed 时说明操作成功！)
查看现在的数据库中存在什么表
mysql> SHOW TABLES;
创建一个数据库表
mysql> CREATE TABLE MYTABLE (name VARCHAR(20), sex CHAR(1));
显示表的结构：
mysql> DESCRIBE MYTABLE;
往表中加入记录
mysql> insert into MYTABLE values (”hyq”,”M”);
用文本方式将数据装入数据库表中（例如D:/mysql.txt）
mysql> LOAD DATA LOCAL INFILE “D:/mysql.txt” INTO TABLE MYTABLE;
导入.sql文件命令（例如D:/mysql.sql）
mysql>use database;
mysql>source d:/mysql.sql;
删除表
mysql>drop TABLE MYTABLE;
清空表
mysql>delete from MYTABLE;
更新表中数据
mysql>update MYTABLE set sex=”f” where name=’hyq’;

全局管理权限对应解释：
FILE: 在MySQL服务器上读写文件。
PROCESS: 显示或杀死属于其它用户的服务线程。
RELOAD: 重载访问控制表，刷新日志等。
SHUTDOWN: 关闭MySQL服务。
数据库/数据表/数据列权限：
ALTER: 修改已存在的数据表(例如增加/删除列)和索引。
CREATE: 建立新的数据库或数据表。
DELETE: 删除表的记录。
DROP: 删除数据表或数据库。
INDEX: 建立或删除索引。
INSERT: 增加表的记录。
SELECT: 显示/搜索表的记录。
UPDATE: 修改表中已存在的记录。
特别的权限：
-
ALL: 允许做任何事(和root一样)。
USAGE: 只允许登录–其它什么也不允许做。

数据库导入
-
MySQL数据库的导入,有两种方法：
先导出数据库SQL脚本，再导入；
直接拷贝数据库目录和文件。
在不同操作系统或MySQL版本情况下，直接拷贝文件的方法可能会有不兼容的情况发生。

所以一般推荐用SQL脚本形式导入。下面分别介绍两种方法。
> 
2. 方法一 SQL脚本形式

操作步骤如下：
2.1. 导出SQL脚本
在原数据库服务器上，可以用phpMyAdmin工具，或者mysqldump命令行，导出SQL脚本。
2.1.1 用phpMyAdmin工具
导出选项中，选择导出“结构”和“数据”，不要添加“DROP DATABASE”和“DROP TABLE”选项。
选中“另存为文件”选项，如果数据比较多，可以选中“gzipped”选项。
将导出的SQL文件保存下来。
2.1.2 用mysqldump命令行
命令格式
mysqldump -u 用户名 -p 数据库名 > 数据库名.sql
范例：
mysqldump -u root -p abc > abc.sql
（导出数据库abc到abc.sql文件）
提示输入密码时，输入该数据库用户名的密码。
2.2. 创建空的数据库
通过主控界面/控制面板，创建一个数据库。假设数据库名为abc，数据库全权用户为abc_f。
2.3. 将SQL脚本导入执行
同样是两种方法，一种用phpMyAdmin（mysql数据库管理）工具，或者mysql命令行。
2.3.1 用phpMyAdmin工具
从控制面板，选择创建的空数据库，点“管理”，进入管理工具页面。
在"SQL"菜单中，浏览选择刚才导出的SQL文件，点击“执行”以上载并执行。
注意：phpMyAdmin对上载的文件大小有限制，php本身对上载文件大小也有限制，如果原始sql文件
比较大，可以先用gzip对它进行压缩，对于sql文件这样的文本文件，可获得5或更高的压缩率。
gzip使用方法：
# gzip xxxxx.sql
得到
xxxxx.sql.gz文件。
提示输入密码时，输入该数据库用户名的密码。
3 直接拷贝
如果数据库比较大，可以考虑用直接拷贝的方法，但不同版本和操作系统之间可能不兼容，要慎用。
3.1 准备原始文件
用tar打包为一个文件
3.2 创建空数据库
3.3 解压
在临时目录中解压，如：
cd /tmp
tar zxf mydb.tar.gz
3.4 拷贝
将解压后的数据库文件拷贝到相关目录
cd mydb/
cp * /var/lib/mysql/mydb/
对于FreeBSD:
cp * /var/db/mysql/mydb/
3.5 权限设置
将拷贝过去的文件的属主改为mysql:mysql，权限改为660
chown mysql:mysql /var/lib/mysql/mydb/*
chmod 660 /var/lib/mysql/mydb/*

Mssql转换mysql的方法
1.导表结构
使用MySQL生成create脚本的方法。找到生成要导出的脚本，按MySQL的语法修改一下到MySQL数据库中创建该表的列结构什么的。
2.导表数据
在MSSQL端使用bcp导出文本文件:
bcp “Select * FROM dbname.dbo.tablename;” queryout tablename.txt -c -Slocalhost\db2005 -Usa
其中”"中是要导出的sql语句,-c指定使用\t进行字段分隔,使用\n进行记录分隔,-S指定数据库服务器及实例,-U指定用户名,-P指定密码.
在MySQL端使用mysqlimport 导入文本文件到相应表中
mysqlimport -uroot -p databasename /home/test/tablename.txt
其中-u指定用户名,-p指定密码,databasename指定数据库名称,表名与文件名相同

备份与恢复
MySQL备份恢复数据的一般步骤
备份一个数据库的例子：
1、备份前读锁定涉及的表
mysql>LOCK TABLES tbl1 READ,tbl1 READ,…
　　如果，你在mysqldump实用程序中使用--lock-tables选项则不必使用如上SQL语句。
2、导出数据库中表的结构和数据
shell>mysqldump --opt db_name>db_name.sql
3、启用新的更新日志
shell>mysqladmin flush-logs
这样可以记录你备份后的数据改变为恢复数据准备。
4、解除表的读锁
mysql>UNLOCK TABLES;
为了加速上述过程，你可以这样做：
shell> mysqldump --lock-tables --opt db_name>db_name.sql; mysqladmin flush-logs
但是这样可能会有点小问题。上命令在启用新的更新日志前就恢复表的读锁，
　　在更新繁忙的站点，可能有备份后的更新数据没有记录在新的日志中。
　　现在恢复上面备份的数据库
1、对涉及的表使用写锁
mysql>LOCK TABLES tbl1 WRITE,tbl1 WRITE,…
2、恢复备份的数据
shell>mysql db_name < db_name.sql
3、恢复更新日志的内容
shell>mysql --one-database db_name < hostname.nnn
假设需要使用的日志名字为hostname.nnn
4、启用新的更新日志
shell>mysqladmin flush-logs
5、解除表的写锁
mysql>UNLOCK TABLES;
```