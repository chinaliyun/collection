分别下载php，mysql，Nginx安装包，都是直接解压出来就可以用不需要安装程序，全部放到d:/wnmp文件夹内

配置nginx
-
进入nginx目录，命令`nginx.exe`就可以直接启动nginx服务器，
```
nginx -t 欲查看nginx的配置文件是否可用
nginx -s reload 重启nginx服务器
nginx -s stop 停止nginx服务器
```
找到默认配置文件，"nginx/conf/nginx.conf"查看默认的端口号，直接在浏览器打开"localhost"查看页面是否正常显示,如果正常显示说明nginx启动成功;

配置文件中找到
```
#location ~ \.php$ {
#    root           html;
#    fastcgi_pass   127.0.0.1:9000;
#    fastcgi_index  index.php;
#    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
#    include        fastcgi_params;
#}
```
全部去掉前面`#`， 并且设置root目录， afstcgi_param由于不确定设置配置文件的时候是否会设置root目录，所以尽可能不要使用$document_root，直接在前面加上文件的根目录即可
比如修改后是这样的：
```
location ~ \.php$ {
    root           E:/Mine/www/cao/server;
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  $document_root/$fastcgi_script_name; //就是这里如果前面没有配置root目录，就不要用@document_root;
    include        fastcgi_params;
}
```
这是为了配置访问php文件时候服务器的设置，把PHP文件的访问全部交给CGI来运行;**注意这里修改后的路径里面去掉了之前的"script",如果没有去掉会报错"no input file special"**






配置/启动php-cgi
-
- CGI 是什么
cgi是一种协议，fastcgi是一种高效执行CGI的管理工具，PHP_FPM是PHP实现了fastCGI的程序，高版本的PHP内已经添加了这个，因此只需要执行php-cgi文件即可。	<a href="http://blog.csdn.net/meegomeego/article/details/36180343">参考文档</a>

- 配置CGI
进入php目录，复制一份"php.ini.developement"文件，并重命名为"php.ini";

打开"php.ini"文件找到下面的配置并修改他们的值:
```
cgi.fix_pathinfo-1
```
`cgi_fix_pathinfo`的目的是为了能正常使用`"SCRIPT_FILENAME"`这个变量具体功能见 <a href="http://www.server110.com/nginx/201404/9901.html">参考文档</a>

进入PHP目录，打开CMD控制台，运行命令`php-cgi -b 127.0.0.1:9000`，以上是运行php-cgi程序并绑定一个端口
在nginx默认的目录下，创建一个新的phpinfo.php文件。内容如下：
```
<?php
	phpinfo();
?>
```
打开浏览器，输入localhost/phpinfo.php，查看是否输出PHP信息；如果成功输出了信息 ，那么就可以随意修改项目的根目录了









安装/配置/启动mysql
=
安装MySQL服务

	进入MySQL/bin目录，使用命令`mysqld.exe --install`安装服务

初始化数据库信息

	mysqld.exe --initialize  --use=root  --console  //控制台中会生成默认的数据库密码

启动MySQL服务

	net start mysql  //;

登录控制台

	mysql -h localhost -u root -p  //，后按照提示输入默认的密码，就登陆成功了。

修改默认密码

	set password=password()  //重设root用户密码











安装/配置/启动phpMyadmin
-

官网下载phpMyadmin压缩包，解压后放到nginx/html目录下，


#### 基础配置

打开phpMyadmin/libiary/config.default.php文件，修改以下设置：


	$cfg['PmaAbsoluteUri'] = 'http://localhost/phpmyadmin';
	$cfg['Servers'][$i]['user'] = 'root';   //mysql登陆账户， 如果不设置需要在登录页面自己输入
	$cfg['Servers'][$i]['password'] = '';	//mysql登陆密码，如果不设置，在登陆页面需要自己输入
	$cfg['Servers'][$i]['auth_type'] = 'cookie'; 	//phpMyadmin打开方式, 建议使用cookie方式，因为更安全
	$cfg['blowfish_secret'] = '';	//短语密码，当auth_type设置为cookie的时候，这一项必须设置，否则页面会提示错误
`$cfg['Servers'][$i]['auth_type'] = 'cookie'`在此有四种模式可供选择，cookie，http，HTTP，config;

config方式即输入phpmyadmin的访问网址即可直接进入，无需输入用户名和密码，是不安全的，不推荐使用。
当该项设置为cookie，http或HTTP时，登录phpmyadmin需要数据用户名和密码进行验证，,具体如下：

	PHP安装模式为Apache，可以使用http和cookie；
	PHP安装模式为CGI，可以使用cookie

#### 打开php的mysql扩展
	
打开php.ini文件，找到`;extension = php_mysqli.dll`字段，去掉前面的分号,打开php的mysqli扩展

如果没有打开phpMyadmin是无法运行的


#### 打开php的mbstring扩展
	
在php.ini文件中找到`;extension=php_mbstring.dll`,字段去掉前面的分号；

如果这里没有打开，phpMyadmin会提示"没有发现 PHP 的扩展设置mbstring"错误



配置批处理/自动化启动和停止
-
在PHP目录下新建php-cgi.vbs文件，内容如下：
```
set wscriptObj = CreateObject("Wscript.Shell")
wscriptObj.run "php-cgi -b 127.0.0.1:9000",0
```
新建runServer.bat文件，内容如下：
```
@echo off

echo Starting nginx...
cd /d %~dp0nginx
start "" "nginx.exe"
echo nginx Started

echo Starting mysql...

cd /d %~dp0mysql/bin
net start mysql

echo Starting PHP FastCGI...
cd /d %~dp0php
start "" "php-cgi.vbs"

pause

Exit
```
新建stopServer.bat文件，内容如下：
```
@echo off
echo Stopping nginx...
taskkill /F /IM nginx.exe > nul

echo Stopping PHP FastCGI...
taskkill /F /IM php-cgi.exe > nul

echo Stopping mysql...
net stop mysql

pause
exit
```
在执行过程中，发现如果不以管理员身份运行，是不允许操作mysql的，但是以管理员身份运行，经常出现找不到文件的情况，经过查找，原因如下：

当以管理员身份运行的时候，目录默认设置为当前的用户目录，而不是bat文件所在目录，所以要在每一个cd命令后面添加`/d`参数 ,这方面内容的支持参考<a href="http://blog.csdn.net/canba/article/details/6628968">这个文档</a>

启动phpmyadmin
-
直接通过浏览器打开localhost/phpMyadmin,输入数据库的用户名和密码，如果配置文件中已经设置，会自动填入，直接点击执行进入即可；

#### 配置phpmyadmin数据库
在phpmyadmin目录下，找到create_tables.sql文件，导入sql文件并执行；

这一步如果没走，phpmyadmin会一直提示"显示高级功能尚未完全设置部分功能未激活". 参考<a href="http://blog.csdn.net/lccee/article/details/54968969">文档</a>
