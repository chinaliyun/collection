安装服务
-
直接进入mysql文件夹内的bin目录，使用命令`mysqld --install`安装mysql服务；

---
问题：出现Install/Remove of the Service denid错误：

解决： 以管理员身份运行cmd
---
服务安装成功会提示 Service Successfily Installed;

启动服务
-
直接使用`net start mysql`命令启动服务
---
问题：MySQL服务无法启动

解决：初始化mysql

初始化mysql
-
使用命令`mysqld --initialize --user=root --console`初始化管理员用户名

初始化成功会在下面提示系统提供的初始化密码，可以直接使用这个密码进入MySQL控制台

进入MySQL控制台
-
使用命令`mysql -h localhost -u root -p`登录MySQL控制台，其中`-h localhost`表示mysql所在IP地址 ，`-u root`表示登录用户名` , `-p`表示用密码登录，后面按照提示输入密码即可登录， 这里登录的时候也可以直接指定要用的数据库名称



