## 1.安装

1、下载：https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.36-winx64.zip

2、解压至F:\myproject\dbs\mysql-5.7.36-winx64

3、创建my.ini配置文件

mysql-5.7.36-winx64目录下：my.ini

```ini
[client]
port=3306
default-character-set=utf8
[mysqld]
#设置为自己MYSQL的安装目录
basedir=F:\myproject\dbs\mysql-5.7.36-winx64\
#设置为MYSQL的数据目录
datadir=F:\myproject\dbs\mysql-5.7.36-winx64\data\
port=3306
character_set_server=utf8
#跳过安全检查
skip-grant-tables
```

4、管理员身份打开cmd，切换到F:\myproject\dbs\mysql-5.7.36-winx64\bin目录下，执行mysqld -install

![image-20211130111400139](C:\Users\a1822\AppData\Roaming\Typora\typora-user-images\image-20211130111400139.png)

报错：找不到MSVCR120.dll。

解决：C:\Program Files\Common Files\microsoft shared\ClickToRun下的MSVCR120.dll、MSVCP120.dll复制到mysql-5.7.36-winx64\bin目录下。

5、初始化数据库：mysqld --initialize-insecure --user=mysql

执行完后会在mysql-5.7.36-winx64目录下生成data

6、启动mysql服务

net start mysql

7、进入mysql管理终端：mysql -u root -p  (当前root用户密码为空)

8、修改root用户密码

```mysql
use mysql; #使用数据库mysql
update user set authentication_string=password('123123') where user='root' and Host='localhost';
flush privileges; #刷新权限
quit
```

9、注销my.ini配置文件中的skip-grant-tables（使登录需要输入密码）

10、重启mysql服务

net stop mysql

net start mysql

11、登录mysql

mysql -u root -p或mysql -u root -pKylin*2020

## 2.创建数据库

```mysql
#创建数据库(默认编码方式为utf8，默认校验规则为utf8_general_ci不区分大小写)
CREATE DATABASE `lh_db01`; ``引起来规避关键字引起的错误
#创建一个使用utf8字符集，并带校对规则(区分大小写)的lh_db02数据库
CREATE DATABASE lh_db02 CHARACTER SET utf8 COLLATE utf8_bin;
#删除数据库
DROP DATABASE lh_db01;
```

## 3.查询数据库

```mysql
#显示数据库语句
SHOW DATABASES
#显示数据库创建语句
SHOW CREATE DATABASE db_name
#数据库删除语句
DROP DATABASE [IF EXISTS] db_name
```

## 4.数据库备份