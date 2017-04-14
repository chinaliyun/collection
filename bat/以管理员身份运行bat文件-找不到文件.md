在配置nginx+php+mysql的批处理任务时候，出现这个问题,简单解释如下：

在执行过程中，发现如果不以管理员身份运行，是不允许操作mysql的，但是以管理员身份运行，经常出现找不到文件的情况，经过查找，原因如下：

当以管理员身份运行的时候，目录默认设置为当前的用户目录，而不是bat文件所在目录，所以要在每一个cd命令后面添加`/d`参数

原理
-
知道windows的bat脚本很强大，但是具体命令向来很少接触，今天在win7上运行自己以前写的一个安装mysql数据库到系统服务的脚本时，遇到一些问题，下面记录一下。<?xml:namespace prefix = o ns = "urn:schemas-microsoft-com:office:office" />
问题：MySQL服务安装脚本在win7下运行失败

原因：win7下，以右键的“以管理员身份运行”默认进入的目录是C:\Windows\System32目录，因此后续的cd Demo_V2.0.4.9命令肯定进入不到正确的目录

解决办法：使用cd /d %~dp0

首先，脚本如下，对具体StartDemo.bat命令不多做解释
```
cd Demo_V2.0.4.9

cd mysql

@echo off

set MYSQL_HOME=%cd%

net stop "DemoMySql"

call "%MYSQL_HOME%\bin\mysqld.exe" remove DemoMySql

call "%MYSQL_HOME%\bin\mysqld.exe" install DemoMySql --defaults-file="%MYSQL_HOME%\bin\my.ini"

net start "DemoMySql"

cd..

cd..
```
这个脚本在xp、2000、2003等系统中都可以正常双击运行。在win7系统中双击运行时，会以普通用户身份运行，此时所获取的文件路径的确是当前路径，而不是C:\Windows\System32。但是运行到卸载以及安装DemoMysql的系统服务时，普通用户显然权限是不够的。

于是在StartDemo.bat右键选择“以管理员身份运行”，此时又会出问题，win7可能出于安全问题考虑，此时获得的目录是C:\Windows\System32，于是后面的执行都会出错或者无效。

此时在脚本开始尝试加入命令cd %cd%，来获取当前路径，实验得知，这行语句在xp等系统中有效，但是在win7中依然无效。得到的目录依然是C:\Windows\System32。

上网查了一下才知道要使用cd /d %~dp0命令来获取脚本所在的目录。在脚本最开始添加cd /d %~dp0即可。之后在xp系统上运行此脚本，确认也没有问题。下面对命令中涉及到的参数做一解释。

问题解释一：关于cd的/d参数
关于cd 的/d参数，在cmd中敲入cd /?

可以看到/d参数的解释如下：

使用 /D 命令行开关，除了改变驱动器的当前目录之外，

还可改变当前驱动器。

这句话貌似不太好理解，我做个试验给大家看就明白了：

通常我们在xp系统中打开cmd窗口时，会显示
```
C:\Documents and Settings\Administrator>
```
如果我们执行如下命令，发现目录依然还是在C:\Documents and Settings\Administrator
```
C:\Documents and Settings\Administrator>cd d:\tomcat6.0.18

C:\Documents and Settings\Administrator>
```
此时，我们键入d:，不但会切换到d盘，而且会切换到d:/tomcat6.0.18的目录
```
C:\Documents and Settings\Administrator>cd d:\tomcat6.0.18

C:\Documents and Settings\Administrator>d:

D:\tomcat6.0.18>
```
下面语句我们就能看到/d参数的作用了。发现加了/d参数之后直接切换到d盘的tomcat6.0.18目录了。
```
C:\Documents and Settings\Administrator>cd /d d:\tomcat6.0.18

D:\tomcat6.0.18>
```
结论：不加/d参数只能在同一驱动器的目录之间切换，加上/d参数则能在不同驱动器之间的目录之间切换

问题解释二：关于%~dp0的批处理命令的详细解释
对此命令并不清楚，以下内容都来自互联网：

%~dp0 “d”为Drive的缩写，即为驱动器，磁盘、“p”为Path缩写，即为路径，目录

cd是转到这个目录，不过我觉得cd /d %~dp0 还好些

选项语法:
```
~0 - 删除任何引号(")，扩充 %0

%~f0 - 将 %0 扩充到一个完全合格的路径名(“f”是file，即文件)

%~d0 - 仅将 %0 扩充到一个驱动器号

%~p0 - 仅将 %0 扩充到一个路径

%~n0 - 仅将 %0 扩充到一个文件名(“n”是name 文件名)

%~x0 - 仅将 %0 扩充到一个文件扩展名

%~s0 - 扩充的路径只含有短名(“s”为Short，短的)

%~a0 - 将 %0 扩充到文件的文件属性(“a”为attribute，即属性)

%~t0 - 将 %0 扩充到文件的日期/时间(“t”time)

%~z0 - 将 %0 扩充到文件的大小(Size 大小)

%~$PATH:0 - 查找列在路径环境变量的目录，并将 %0 扩充到找到的第一个完全合格的名称。如果环境变量名未被定义，或者没有找到文件，此组合键会扩充到空字符串可以组合修饰符来得到多重结果:

%~dp0 - 仅将 %0 扩充到一个驱动器号和路径

%~nx0 - 仅将 %0 扩充到一个文件名和扩展名

%~fs0 - 仅将 %0 扩充到一个带有短名的完整路径名

%~dp$PATH:0 - 查找列在路径环境变量的目录，并将 %I 扩充到找到的第一个驱动器号和路径。

%~ftza0 - 将 %0 扩充到类似输出线路的 DIR

%0为当前批处理文件

如果0换成1为第一个文件，2为第2个

****************************************************

%0代指批处理文件自身

%~d0 是指批处理所在的盘符

%~dp0 是盘符加路径

cd %~dp0 就是进入批处理所在目录了
```