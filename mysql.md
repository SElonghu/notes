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

## 6.列类型

### 6.1数值类型

#### 6.1.1整型

1、tinyint[1个字节]

2、smallint[2个字节]

3、mediumint[3个字节]

4、**int[4个字节]**

5、bigint[8个字节]

```mysql
#如何定义一个无符号的整数
create table t1 (id tinyint);  #默认是有符号的
create table t2 (id tinyint unsigned); #无符号的
```

#### 6.1.2小数类型

1、float [单精度4个字节]

2、**double [双精度8个字节]**

3、**decimal[M,D] [大小不确定，M指定长度，D指定小数点后位数]**

#M最大65，D最大30。如果D被省略，默认是0，若M被省略，默认是10。**比bigint unsigned存放整数位还多**。

```mysql
create table t2 (num1 float, num2 double, num3 decimal(30,20));
insert into t2 values(8.88888,8.88888,8.88888);
#float四舍五入保留小数4位8.8889,double四舍五入保留小数14位。
```



#### 6.1.3比特类型

1. bit(m) m 在 1-64
2. 显示按照bit

```mysql
create table t1 (num bit(8));  //8代表位数
insert into t1 values(3);
```

### 6.2文本类型

size填字符数、

1、**char(size) 0-255**字符

//固定长度字符串，最大255字符

//char(4)即使插入'aa'，也会占用分配的4个字符空间。

2、**varchar(size) 0-65535字节 [0~2^16-1]**  但是其中有1~3个字节作为保留字节，用于存储该字符大小

//可变长度字符串，最大65532字节。能存放的字符数与编码有关，utf8编码3字节=1字符，gbk编码2字节=1字符。

//varchar(4)插入'aa'，会占用(实际数据大小)+(1~3)字节

3、**text [0~2^16-1]**字节

4、longtext [0~2^32-1]字节



**char和varchar对比**：

1.如果数据定长，推荐使用char，如md5、邮编、手机号、身份证号。

2.如果字段长度不确定，则使用varchar，比如留言、文章。

3.查询速度：char>varchar

4.存放文本时，也可以用text类型。注意text不能有默认值，大小0-2^16字节。或MEDIUMTEXT 0~2^24字节、LONGTEXT 0~2^32字节。

5.char、varchar中1字符=1汉字=1字母=多字节；text中1字母（数字）=1字节，1汉字=多字节

### 6.3二进制类型

1、blob [0~2^16-1]

2、longblob [0~2^32-1]

### 6.4日期类型

1、date [日期：年月日]

2、time [时间：时分秒]

3、**datetime [年月日 时分秒 YYYY-MM-DD HH:MM:ss]**

4、**timestamp [时间戳]**

5、year [年]

```mysql
create table t4 (
	birthday date,    -- 生日
    job_time datetime,  -- 记录年月日 时分秒
    login_time timestamp -- 时间戳
    	not null default current_timestamp  -- login_time不为空，默认位当前时间
    	on update current_timestamp); -- 更新这条数据时同时更新时间戳为当前时间
#插入一条数据，时间戳login_time会变成当前时间
insert into t4 (birthday, job_time) values('2022-11-11', '2022-11-11 10:10:10');
```

## 7.表操作

### 7.1添加列

```mysql
ALTER TABLE tablename
ADD	(column datatype [DEFAULT expr]   
);
#在emp表中的resume字段后面添加一个字段image，不为空，默认为''
ALTER TABLE emp
	ADD image VARCHAR(32) not null default '' after resume;
```

### 7.2修改列

```mysql
#修改emp表中的字段job为varchar(60)
ALTER TABLE emp
	MODIFY job VARCHAR(60) 	NOT NULL DEFAULT '';
```

### 7.3删除列

```mysql
#删除emp表中的sex字段
ALTER TABLE emp
	DROP sex;
```

### 7.4修改表名

```mysql
#修改表名emp为employee
RENAME TABLE emp TO employee;
```

### 7.5修改字符集

```mysql
#修改表employee的字符集为utf8
ALTER TABLE employee CHARACTER SET utf8;
```

### 7.6修改列名

```mysql
#修改表employee的name字段名改成user_name
ALTER TABLE employee 
	CHANGE `name` `user_name` VARCHAR(32) NOT NULL DEFAULT '';
```

## 8.CRUD语句

### 8.1INSERT语句

```MYSQL
INSERT INTO `goods` (id, goods_name, price)
	values(10, '华为手机', 2000);
```

**注意事项**：

1. 插入的数据应与字段的数据类型相同，如：把'abc'添加到int类型会报错。但是'30'添加到int类型会正常转换成30。

2. 数据的长度应在列的规定范围内，例如：不能将一个长度为80的字符串加入到长度为40的列中。

3. 在values中列出的数据位置一般与被加入的列的排列位置相对应。

4. 字符和日期类型数据应包含在单引号中。

5. 列可以插入空值（前提是该字段允许为空），insert into `goods` (id, goods_name, price) values(40, 'vivo手机', null);

6. insert into tab_name (列名...) values (), (), () 形式添加多条记录。

   ```mysql
   insert into `goods` (id, goods_name, price)
   	values(50, '三星手机', 2300), (60, '海尔手机', 1800);
   ```

7. 如果是给表中所有字段添加数据，可以不写前面的字段名称。

   ```mysql
   insert into `goods`
   	values(40, 'vivo手机', null);
   ```

8. 默认值的使用，当不给某个字段值时，如果有默认值就会添加，否则报错。

   ```mysql
   insert into `goods` (id, goods_name)
   	values(80, '格力手机');
   #此时price字段会添加默认值
   ```

### 8.2update语句

UPDATE用来更新原有的字段；SET子句用来指示需要修改哪些列和修改的值；WHERE子句用来指定需要修改的行（记录），如果没有则更新所有行；

```mysql
UPDATE table_name
	SET col_name1=expr1 [, col_name2=expr2 ...]
	[WHERE where_definition]
#例如，将employee表中小妖怪的薪水改成3000，若不加where则会将所有人的salary都改成3000。
UPDATE employee
	SET salary = 3000
	WHERE user_name = '小妖怪';
#将老妖怪薪水原有基础上增加1000元
UPDATE employee
	SET salary = salary + 1000
	WHERE user_name = '老妖怪';
```

### 8.3delete语句

```mysql
DELETE FROM table_name
	[WHERE where_definition];
#例如，删除表employee中名称为‘老妖怪’的记录，如果不加where则会删除表的所有记录。
delete from employee
	where user_name = '老妖怪';
```

细节：

1.如果不加where则会删除表的所有记录。

2.delete语句只能删除整条记录，不能删除某一列。

3.delete语句仅删除记录，不删除表本身。如要删除表本身，使用drop table 表名。

### 8.4select语句

```mysql
SELECT [DISTINCT] *|{column1,column2,column3..}
	FROM table_name;
```

注意：

1.select指定查询哪些列的数据。

2.column指定列名。

3.*号代表查询所有列。

4.from指定查询哪张表。

5.DISTINCT可选，是否去掉重复数据。