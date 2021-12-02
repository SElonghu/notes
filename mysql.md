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

### 4.1备份恢复整个数据库

```shell
#备份数据库（注意：在Dos中执行）
mysqldump -u 用户名 -p -B 数据库1 数据库2 数据库n > D:\\文件名.sql
```

```mysql
#恢复数据库（注意：进入Mysql命令行再执行）
source 文件名.sql;
#恢复数据库也可以通过把文件名.sql里的sql语句直接执行的方式
```

### 4.2备份恢复表

```shell
#备份数据库表（注意：在Dos中执行）
mysqldump -u 用户名 -p 数据库1 表1 表2 表n > D:\\文件名.sql
```

```mysql
#恢复数据库表（注意：进入具体数据库再执行）
use db1; #进入数据库db1
source 文件名.sql
```



## 5.创建表

```mysql
use db1;   #先切换到一个数据库
CREATE TABLE table_name
(
    filed1	datatype,
    filed2	datatype,
    filed3	datatype
)character set 字符集 collate 校对规则 engine 存储引擎;
#character set: 如不指定则为所在数据库字符集
#collate：如不指定则为所在数据库校对规则
#engine: 引擎（详情见后面章节）
```

```mysql
#例子
CREATE TABLE `user`
(
    id	int,
    `name`	varchar(255),
    `password`	varchar(255),
    `birthday` date
)character set utf8 collate utf8_bin engine innodb;
```

