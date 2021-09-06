# 一、安装

安装的是5.7.19免安装版。现在已经有了8.0以上的版本，分为收费版和社区版了，公司大部分还是使用5.7版本的。

## 1.1安装步骤

- 特别说明

  如果安装过程中，出错了或者想重新再安装一遍：

  `sc delete mysql`

  删除已经安装好的mysql服务，需慎重。

（1）下载后得到zip安装文件

（2）解压的路径最好不要有中文和空格，尽量选一个空间足够大的

（3）添加环境变量，在path环境变量中增加mysql的安装目录\bin目录。

![image-20210506143337018](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506143337018.png)

（4）在D:\MySQL\mysql-5.7.19-winx64目录下创建my.ini文件，5.7版本的需要我们自己创建，现在最新版都是自动生成了。

```ini
[client]

port=3306

default-character-set=utf8

[mysqld]

#设置为自己MYSQL的安装目录

basedir=D:\MySQL\mysql-5.7.19-winx64\

#设置为MYSQL的数据目录，这个目录是系统创建的

datadir=D:\MySQL\mysql-5.7.19-winx64\data\

port=3306

character_set_server=utf8

#跳过安全检查

skip-grant-tables
```

（5）使用管理员身份打开cmd，并切换到cd /D D:\MySQL\mysql-5.7.19-winx64\bin目录下，执行

`mysqld -install`

（6）初始化数据库：

`mysqld --initialize-insecure --user=mysql`

至此，安装目录下会由系统生成一个data文件夹：

![image-20210506121720476](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506121720476.png)

data文件夹中会有三个子文件夹：

![image-20210506121751775](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506121751775.png)

说明初始化成功。

（7）启动mysql服务

`net start mysql`

![image-20210506125925975](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506125925975.png)

启动成功说明安装成功。

停止mysql使用如下指令：

`net stop mysql`

![image-20210506130128366](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506130128366.png)

（8）进入mysql管理终端：

`mysql -u root -p`

注意：当前root用户密码为空。-u和root之间最好打一个空格。

（9）修改root用户密码：

```sql
use mysql;

update user set authentication_string=password('lsx')where user='root' and Host='localhost';
#上面的语句就是修改root用户的密码为“lsx”，从本地登陆
#注意：在后面需要带分号，回车即可执行该指令
#执行：flush privileges;刷新权限
#退出：quit
```

（10）修改my.ini，再次进入就会进行权限验证了

`#skip-grant-tables`

（11）重新启动mysql

```
net stop mysql
net start mysql
#提示：该指令需要退出mysql，在Dos下执行
```

(12)如果真的错误了，清除mysql服务，再次安装。清除就是：

`sc delete mysql`

## 1.2命令行连接mysql

mysql本质上是一个服务，启动后会在某个端口（默认是3306）监听来自客户端的请求。

链接到mysql服务的指令：

`mysql -h 主机IP -P 端口 -u 用户名 -p密码`

注意：

- -p密码不要有空格
- -p后面没有写密码，回车会要求输入密码
- 连接之前，要确保mysql服务已启动
- 如果没有写-h 主机，默认就是本机
- 如果没有写-P 端口，默认就是3306；实际工作中安全起见，一般都会修改端口，不用3306

![image-20210506143915665](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506143915665.png)

连接成功后，可以通过下面这条指令查看数据库内容：

`show databases;`

注意后面有分号不要忘记。

## 1.3图像化mysql管理软件

常用的管理软件有两个：NAVIcat（付费软件）、

### 1.3.1NAVIcat

#### 新建连接

安装过程自行学习。

安装好之后，可以点击连接来新建一个连接：

![image-20210506145153709](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506145153709.png)

![image-20210506145217347](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506145217347.png)

可以发现，连接过程中指定的主机、端口、用户名、密码等等，跟命令行链接都是一样的，只是以图形化方式来呈现。

设置好之后，直接点确定即可建立连接，并可通过右键打开连接查看：

![image-20210506145606807](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506145606807.png)

此时便可以操作数据库，完成新建表等等。

命令行和图形化方式各有利弊，不能说谁更好，比如图形化方式不方便程序控制，更适合非程序员来操作，而命令行方式会比较繁琐。

### 1.3.2SQLyog

略

## 1.4破除神秘

### 1.4.1数据库三层结构

- 所谓安装mysql数据库，就是在主机安装一个数据库管理系统（DBMS，database manage system），这个管理程序可以管理多个数据库;
- 一个数据库中可以创建多个表，以保存数据（信息）;
- 数据库管理系统（DBMS）、数据库和表的关系如图所示：

![image-20210506163352248](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506163352248.png)

mysql数据库-普通表的本质仍然是文件，这样才能实现数据持久化。

文件位置也要知道在哪里保存。

### 1.4.2SQL语句分类

严格来说，sql并不是一门编程语言，它只是一个取数工具，与它的原意（结构化查询语言）比较贴切。sql语言可以大致分为以下几类：

- DDL：数据定义语句[create 表，库...]
- DML：数据操作语句[增加insert，修改update，删除delete]
- DQL：数据查询语句[select]
- DCL：数据控制语句[管理数据库：比如用户权限grant revoke（撤销）]

实际开发中，更多的还是通过高级语言，例如Java、golang等来操作数据库的。

# 二、数据库与表

## 2.1创建数据库

### 基本指令

CREATE DATABASE [<u>IF NOT EXISTS</u>] db_name [create_specification [,craete_specification]... ]

create_specification（可选参数）:

​	[DEFAULT] CHARACTER SET charset_name

​	[DEFAULT] COLLATE collation_name

- CHARACTER SET：指定数据库采用的字符集，如果不指定字符集，默认utf8
- COLLATE：指定数据库字符集的校对规则（常用的utf8_bin（区分大小写）、utf8_general_ci （不区分大小写）注意默认是utf8_general_ci）[举例说明 database.sql 文件]

### 练习

1. 创建一个名为lsx_db01的数据库（图形化和指令）

   - 图形化方式略，右键当前连接即可

   - 使用指令创建数据库

     ```sql
     #使用指令创建数据库，后面分号不要忘记
     CREATE DATABASE lsx_01;
     #注意，在创建数据库和表的时候，为了规避关键字，可以使用反引号解决。
     #例如：CREATE DATABASE `CREATE`,创建一个叫做“CREATE”的数据库
     
     #删除数据库指令
     DROP DATABASE lsx_01;
     ```

     

2. 创建一个使用utf8字符集的lsx_db02数据库

   ```sql
   CREATE DATABASE lsx_02 CHARACTER SET utf8;
   ```

   

3. 创建一个使用utf8字符集，并附带校对规则的lsx_db03数据库

   ```sql
   CREATE DATABASE lsx_03 CHARACTER SET utf8 COLLATE utf8_bin;
   ```

如果表没有指定字符集和校对规则，则使用的是这个表所属数据库的字符集和校对规则。

### Q&A

不同的校对规则有什么影响？

可能会影响查询结果。若不区分大小写，则会将所有查询到的结果显示出来，包括大写和小写。

## 2.2查看、删除数据库

### 基本指令

```sql
#显示数据库语句：
SHOW DATABASES;
#显示某个数据库中的表：
use database; #切换到某个数据库中
show tables;

#显示数据库创建语句：
SHOW CREATE DATABASE db_name;

#数据库删除语句（慎用）
DROP DATABASE [IF EXISTS] db_name;
#删除数据库中的表
DROP TABLE [IF EXISTS] tab_name;
```

### 练习

1. 查看当前数据库服务器中的所有数据库
2. 查看前面创建的lsx_01数据库的定义信息
3. 删除前面创建的lsx_01数据库

## 2.3备份和恢复数据库

设想这样两种场景：

- 我们需要将一台服务器上的DBMS管理的数据库，迁移至两外一台服务器上的DBMS，此时需要将第一台主机上的数据库备份，然后拿到另外一台服务器上恢复；
- 某些数据库十分重要，需要时不时备份到文件系统。一旦这些数据库信息被破坏，还有机会恢复。

### 基本指令

```sql
#备份数据库（注意：在dos执行）命令行！！！
mysqldump -u 用户名 -p -B 数据库1 数据库2 数据库n > 文件名.sql
#生成的sql文件内部就是一系列的sql语句

#备份数据库中的表
mysqldump -u 用户名 -p密码 数据库 表1 表2... 表n > d:\\文件名.sql
#注意：上述密码直接写，回车之后就不会提示输入了。也不要带-B，否则会认为表名是数据库名

#恢复数据库（注意：进入mysql命令行再执行）
Source文件名.sql

#第二个恢复方法：复制生成的sql文件中的语句，放到查询编辑器中，全部执行一遍。
```

### 练习

1. databases03.sql备份lsx_02和lsx_03库中的数据，并恢复。
2. 备份某个数据库中的某张表，并恢复

### 作业

安装ecshop数据库

1. 这是一个ecshop的数据库，包括ecshop所有的表，请导入到mysql数据库中备份

2. 将ecshop整个数据库备份到你的d:\\ecshop.sql

   提示：到dos下执行：

   `mysqldump -u root  -p密码 -B ecshop > d:\\ecshop.sql`

3. 将mysql的ecshop数据库删除，并通过备份的d:\\ecshop.sql恢复

## 2.4创建表

### 2.4.1基本指令

```sql
CREATE TABLE table_name
(
	field1 datatype,
	field2 datatype,
	field3 datatype
)character set 字符集 collate校对规则 engine 存储引擎
#field-指定列名（字段名） datatype-指定列类型（字段类型）
#character set：如不指定则为所在数据库字符集
#collate：如不指定则为所在数据库校对规则
#engine:引擎（这个涉及内容较多，后面补充）
#若表名较长，可用下划线分割
```

注意：在lsx_02创建表时，要根据需保存的数据创建相应的列，并根据数据的类型定义相应的列（字段）类型。

例如，user表：

id 			整型

name		字符串

password	字符串

birthday		日期

图形化的形式创建表比较简单，自己动手试一下即可，实际开发中更多的是使用指令来创建表，这个必须掌握。

```sql
#首先打开lsx_02这个数据库
use lsx_02

#执行如下指令创建表
CREATE TABLE `user`(
	id INT,
	`name` VARCHAR(255),
	`password` VARCHAR(255),
	`birthday` DATE)
	CHARACTER SET utf8 COLLATE utf8_bin ENGINE INNODB;
```

出现如下结果，说明创建成功：

![image-20210506201238566](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506201238566.png)



## 2.5mysql常用数据类型（列类型）

### 2.5.1概览

mysql的列类型，即mysql的数据类型，习惯叫法不同。

![image-20210506201726018](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506201726018.png)

常用的类型如下：

数值类型：

![image-20210506203435856](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506203435856.png)

常用的有：int、double、decimal（精度高，适合做报表类）

其他类型：

![image-20210506203229525](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210506203229525.png)

常用的有char、varchar、text、datetime、timestamp（提高效率）

详细可以参考mysql参考手册。

### 2.5.2数值类型

注意：在满足要求的情况下，尽量选择占用空间小的类型。

```sql
#演示：使用tinyint 来演示范围
#说明：表的字符集，校验规则，存储引擎均使用默认
#1.如果没有指定unsigned，则TINYINT就是有符号
#2.如果指定 unsigned，则tinyint就是无符号0~255
CREATE TABLE t3(
	id   TINYINT); # 创建表t3
INSERT INTO t3 VALUES(255);	
# ERROR 1264 (22003): Out of range value for column 'id' at row 1
# 这是非常简单的添加语句，超出TINYINT范围，会插入不进去
INSERT INTO t3 VALUES(122);	# Query OK, 1 row affected (0.00 sec) 插入成功

CREATE TABLE t4(
	id   TINYINT UNSIGNED); # 创建表t4，指定id为无符号tinyint
INSERT INTO t4 VALUES(255);	# Query OK, 1 row affected (0.00 sec) 插入成功

```

#### 位类型（bit）

位类型使用的不多，这里仅通过一个案例演示。

```sql
#1.bit(m) m在1~64之间
#2.添加数据 范围按照给定的位数来确定，比如m = 8表示一个字节 0~255
#3.显示的时候，按照bit来显示
CREATE TABLE t5 (num BIT(8));
INSERT INTO t5 VALUE(3); # 插入数字3
SELECT * FROM t5; #按位来显示
```

查询结果如下：

![image-20210507115030609](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210507115030609.png)

细节说明：

- bit字段显示时，按照位的方式显示

- 查询的时候仍然可以使用添加的数值来查找，

  如上述例子中，可以这样来查：`SELECT * FROM t5 WHERE num = 3;`

- 如果一个值只有 0 , 1 可以考虑使用bit(1)，可以节约空间

- 位类型，M指定位数，默认为1，范围1~64

- 使用场景不多

#### 小数

1. FLOAT/DOUBLE [UNSIGNED] 

Float单精度，Double双精度。

2. DECIMAL[M,D] [UNSIGNED]
   - 可以支持更加精确的小数位。M时小数位数（精度）的总数，D时小数点（标度）后面的位数。
   - 如果D是0，则值没有小数点或分数部分，M最大65（数字最长能表示65位）。D最大是30（小数部分最多能有30位）。如果D被省略，默认是0，如果M被省略，默认是10。
   - 建议：如果希望小数的精度高，推荐使用decimal
3. 案例演示

```sql
#演示decimal类型、float、double类型的使用
#创建表，添加字段
CREATE TABLE t6(
	num1 FLOAT, 	#float字段
    num2 DOUBLE, 	#double字段
    num3 decimal(30,20));	#decimal字段
#添加数据
INSERT INTO t6 VALUES(88.12345678912345,88.12345678912345,88.12345678912345);
SELECT * FROM t6;
#以上查询的时候，超出范围的小数部分自动省略，不足的部分用0补齐
#+---------+-------------------+-------------------------+
| num1    | num2              | num3                    |
+---------+-------------------+-------------------------+
| 88.1235 | 88.12345678912345 | 88.12345678912345000000 |
+---------+-------------------+-------------------------+
#decimal可以存放很大的数
CREATE TABLE t7(
    num decimal(65));
INSERT INTO t7 VALUES(899553131345431321354321321); 
# Query OK, 1 row affected (0.01 sec)
# 这已经远远超出了bigint能表示的范围
```

### 2.5.3字符串类型

#### 基本用法

- CHAR(size)

  固定长度字符串，最大255字符

- VARCHAR(size)

  可变长度字符串，最大65532字节 【utf8编码最大21844字符 1~3个字节用于记录大小】

注意字符和字节的区别，不同编码格式中，字符所占的字节是不同的，

如utf8编码，3个字节表示一个字符，那么最大字符就是 65532 / 3 = 21844；

而gbk编码，2个字节表示一个字符，最大字符就是 65532 / 2 = 32766

#### 应用案例

```sql
#字符串类型 char  varchar 的使用
#注释的快捷键是 shift + ctrl + c，取消注释是shift + ctrl + r
-- CHAR(size)
-- 固定长度字符串 最大255 字符
--为了方便，后续关键字都用小写字母表示
--varchar(size) 0~65535字节
--可变长度字符串  最大65532字节
--如果表的编码是utf8 varchar(size) size = (65535-3) / 3 = 21844
--如果表的编码是gbk varchar(size) size = (65535-3) / 2 = 32766
create table t9(
	`name` char(255));
create table t10(
	`name` varchar(255));	#这里最大是21844
create table t11(
	`name` varchar(32766)) cahrset gbk;	#这里最大是32766

```

#### 细节说明

- char(4)：这个4表示字符数（最大255），不是字节数，不管是中文还是英文都是放4个，按字符计算
- varchar(4)：这个4表示字符数，不管是字母还是英文都以定义好的表的编码来存放数据。所以说它是变长的，这4个字符到底占用多少个字节，取决于表的编码设置

```sql
# 字符串 char
create table t11(
	`name` char(4));
insert into t11 values('12345'); 
# ERROR 1406 (22001): Data too long for column 'name' at row 1
# 根据字段设置，这里最多只能插入4个字符
insert into t11 values('abcd');  # Query OK, 1 row affected (0.00 sec) 插入成功

#字符串 varchar
create table t12(
	`name` varchar(4));
insert into t12 values('1234');

```

- char(4)是定长，就是说，即使你插入"aa",也会占用分配的4个字符的空间。

- varchar(4)是变长，就是说，如果你插入了"aa",实际占用空间大小并不是4个字符，而是按照实际占用空间来分配（注意：varchar本身还需要占用1~3个字节来记录存放内容的长度）

  L(实际数据大小) + （1~3预留字节）= 实际占用空间

- 什么时候使用char，什么时候使用varchar呢？

  如果数据是定长，推荐使用char，比如md5的密码，邮编，手机号，身份证号等等，char(32)

  如果一个字段的长度不确定，推荐使用varchar，比如留言、文章等等。

  查询速度：char > varchar

- 在存放文本时，也可以使用Text数据类型，可以将text列视为varchar列，注意TEXT不能有默认值，大小 0~2^16字节

- 如果希望存放更多字符，可以选择mediumtext 0~2^24 或者longtext 0~2^32

```sql
create table t13(
	content text,
	content2 mediumtext,
	content3 longtext);
insert into t13 values('床前明月光','疑是地上霜','举头望明月');
select * from t13;
#+-----------------+-----------------+-----------------+
| content         | content2        | content3        |
+-----------------+-----------------+-----------------+
| 床前明月光      | 疑是地上霜      | 举头望明月      |
+-----------------+-----------------+-----------------+
```

### 2.5.4日期类型

#### 基本使用

```sql
create table birthday6(
	t1 date,
    t2 datetime,
    t3 timestamp not null default current_timestamp on update
    current_timestamp); #timestamp时间戳
#上述代码意思也就是没有设定时间戳的情况下，会以当前时间作为时间戳  
#not null default 是默认不允许为空的意思
insert into birthday(t1,t2) values('2022-11-11','2022-11-11 10:10:10');

```

#### 细节说明

- timestamp在insert和update时，自动更新。

```sql
#时间相关类型
#创建一张表，包含date , datetime , timestamp 等字段
create table t14(
	birthday date,	#生日
	job_time datetime,	#记录年月日 时分秒
	login_time timestamp 
    not null default current_timestamp on update
    current_timestamp);	#登录时间，如果希望login_time列自动更新，需要配置

insert into t14(birthday,job_time) values('2022-11-11','2022-11-11 10:10:10');
#上述语句中，只给前两列插入了数值，第三列会自动以当前时间来填充
select * from t14; --查询
#+------------+---------------------+---------------------+
| birthday   | jobtime             | login_time          |
+------------+---------------------+---------------------+
| 2022-11-11 | 2022-11-11 10:10:10 | 2021-05-07 17:51:07 |
+------------+---------------------+---------------------+
#如果我们更新了 t14表的某条记录，login_time列会自动以当前时间进行更新
insert into t14(birthday,job_time) values('2022-12-12','2022-12-12 10:10:10');
select * from t14; --查询
#+------------+---------------------+---------------------+
| birthday   | jobtime             | login_time          |
+------------+---------------------+---------------------+
| 2022-11-11 | 2022-11-11 10:10:10 | 2021-05-07 17:51:07 |
| 2022-12-12 | 2022-12-12 10:10:10 | 2021-05-07 17:54:06 |
+------------+---------------------+---------------------+
```

### 2.5.5数据类型练习

创建一个员工表emp，选用适当的数据类型。

![image-20210507200838016](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210507200838016.png)

```sql
#创建表的课堂练习
-- 字段 	属性
--id 	  整型
--name	  字符型
--sex	  字符型
--birthday	日期型(date)
--entry_date	日期型(date)
--job	  	字符型
--salary	小数型
--resume	文本型
create table `emp`(
	id int,
	name varchar(32),
	sex char(1),
	birthday date,
	entry_date datetime,
	job varchar(32),
	salary double,
	`resume` text) charset utf8 collate utf8_bin engine innodb;
#添加一条记录
insert into `emp`
	values(23,'goku','男','1985-08-08','1995-08-08 11:11:11','赛亚人',3500,'龙珠男主角');

#查看记录是否添加成功
select * from `emp`;
#+------+------+------+------------+---------------------+-----------+--------+-----------------+
| id   | name | sex  | birthday   | entry_date          | job       | salary | resume          |
+------+------+------+------------+---------------------+-----------+--------+-----------------+
|   23 | goku | 男   | 1985-08-08 | 1995-08-08 11:11:11 | 赛亚人    |   3500 | 龙珠男主角      |
+------+------+------+------------+---------------------+-----------+--------+-----------------+
```

## 2.6修改表

图形化方式修改表比较简单，自己尝试即可。

前面已经讲过，实际开发过程中，往往是通过程序发送指令到dbms来修改表内容的，

并且实际开发中，有时候会有无权通过界面连接数据库的情况，所以学习命令行修改表的操作还是很有必要的。

### 2.6.1基本操作

```sql
#使用alter table语句追加，修改，或删除列的语法
#alter是修改的意思
--添加列
--1.在表的最后一列新增一列
alter table tablename
add (column datatype [default expr]
	[,column datatype]...);
--2.在指定的位置增加一列

#修改列
alter table tablename
modify (column datatype [default expr]
       [,column datatype]...);
       
#删除列
alter table tablename
drop column;

#查看表的结构，显示表的所有列
desc tablename;

#修改表名
rename table tablename to new_name;

#修改表字符集
alter table tablename character set 字符集;

#修改列名
alter table tablename change column_name new_name datatype;

#删除数据库中的表
drop table [if exists] tab_name;

```

### 2.6.2练习

- 员工表emp上增加一个wife列，varchar类型，要求在birthday后面（笔记这段代码执行不出来，注意查错！）；

  `alter table emp add(wife varchar(32)) not null default '' after birthday;`

  其中，not null default ' ' 表示不允许为空，默认给一个空串。

- 修改job列，使其长度为4；

  `alter table characters modify job varchar(4);`

- 表名改为characters；

  `rename table emp to characters;`

- 修改表的字符集为utf8；

  `alter table characters character set utf8;`

- 列名name修改为user_name;

  `alter table characters change salary power int;`

- 删除wife列；

  `alter table characters drop wife;`

# 三、数据库CRUD语句

C -- create

R -- read

U -- update

D --delete

**写sql语句的前提是对表结构十分清楚，否则是绝对写不出来sql语句的！！！**

**写sql语句的前提是对表结构十分清楚，否则是绝对写不出来sql语句的！！！**

**写sql语句的前提是对表结构十分清楚，否则是绝对写不出来sql语句的！！！**

## 3.1Insert

### 3.1.1基本用法

使用insert语句向表中插入数据。

```sql
insert into table_name [(column [,column...])]
	values  (value [,value...]);
#上述值和列是一一对应的关系，对应的值插入到对应列中
```

练习：

```sql
--创建一张商品表goods (id int , goods_name varchar(10),price double);
create table goods(
	id int,
	goods_name varchar(10),
	price double);
--添加2条记录
insert into goods values(001,"huawei",4999);
insert into goods values(002,'iphone',6299);
--查看添加效果
select * from goods;
#+------+------------+-------+
| id   | goods_name | price |
+------+------------+-------+
|    1 | huawei     |  4999 |
|    2 | iphone     |  6299 |
+------+------------+-------+
--关于默认值
create table goods2(
	id int,
	goods_name varchar(10),
	price double not null default 100); #默认价格是100
--查询
select * from goods2;
#+------+------------+-------+
| id   | goods_name | price |
+------+------------+-------+
|    1 | huawei     |   100 |
+------+------------+-------+
```

### 3.1.2细节说明

1. 插入的数据应与字段的数据类型相同，

   比如把'abc'添加到int类型会错误

2. 数据的长度应在列的规定范围内，例如：不能将一个长度为80的字符串加入到长度为40的列中

3. 在values中列出的数据位置必须与被加入的列的排列位置相对应

4. 字符和日期型数据应包含在单引号中

5. 列可以插入空值，前提是该字段允许为空（不写not null，则表示可以为空），`insert into table value(null)`

6. insert into tab_name(列名...) values (),(),()形式添加多条记录

   `insert  into goods values (003,'xiaomi',3000),(004,'vivo',2599);`

7. 如果是给表中的所有字段添加数据，可以不写前面的字段名称

8. 默认值的使用：当不给定某个字段值时，如果有默认值就会添加，否则报错。具体来讲，

   如果某个列没有指定not null，那么当添加数据时，没有给定值，则会默认给null；

   如果我们希望指定某个列的默认值，可以在创建表时指定


## 3.2update语句

update语句用来修改表中的数据

```sql
update tab_name
	set col_name1 = expr1 [,col_name2 = sepr2 ...]
	[where where_definition]
--where条件如果不写，默认会修改表中所有记录	
```

练习

修改之前创建的characters表中的记录：

```sql
#将characters表名修改为men
rename table characters to men;
#将所有角色job改为“战士”
#如果没有带where条件，会修改所有的记录，因此要小心
update men set job = "战士";

#也可以同时修改多个列的值
update men set resume = "打败魔人布欧",sex = "♂ ";

#将姓名为goku的power修改为8000
update men set power = 8000 where name = "goku";

#将贝吉塔的power在原有基础上增加1000
update men set power = power + 1000 where name = "贝吉塔";
```

## 3.3delete语句

### 3.3.1基本用法

delete语句用来删除表中的数据。

```sql
delete from tb_name [where where_definition]
```

练习

```sql
#删除表中名为“贝吉塔”的记录
delete from men where name = "贝吉塔";

#删除表中所有记录，这个操作一定要小心！
delete from men;
```

### 3.3.2细节说明

- 如果不使用where子句，将删除表中所有数据
- delete语句不能删除某一列（可使用update 设置为 null 或者""）
- 使用delete语句仅删除记录，不删除表本身，如果要删除表本身，使用drop table语句。

## 3.4select语句（**重要**）

select语句是考察一个程序员能力的很好的标准。

### 3.4.1基本语法

```sql
select [distinct] * | {column1 , column2, column3...}
	from tablename
```

解释

- select指定查询哪些列的数据
- column指定列名
- *号代表查询所有列
- from指定查询哪张表
- distinct可选，指显示结果时，是否去掉重复数据

练习

```sql
#首先创建测试用表
create table student(
	id int not null default 1,
	name varchar(20) not null default '',
	chinese float not null default 0.0,
	english float not null default 0.0,
	math float not null default 0.0
);

insert into student(id,name,chinese,english,math) values(1,'孙悟空',89,78,90);
insert into student(id,name,chinese,english,math) values(2,'贝吉塔',67,98,56);
insert into student(id,name,chinese,english,math) values(3,'比克',87,78,77);
insert into student(id,name,chinese,english,math) values(4,'克林',88,98,90);
insert into student(id,name,chinese,english,math) values(5,'孙悟饭',82,84,67);
insert into student(id,name,chinese,english,math) values(6,'特南克斯',55,85,45);
insert into student(id,name,chinese,english,math) values(7,'孙悟天',75,65,30);
insert into student(id,name,chinese,english,math) values(6,'特南克斯',55,85,45);

#查询表中所有学生的信息
select * from student;

#查询表中所有学生的姓名和对应的英语成绩
select name,english from student;

#过滤表中重复数据
select distinct * from student;

#要查询的记录，每个字段都相同，才会去重
#例如查询英语成绩，那么英语成绩相同时就可以去重：
select distinct english from student;
#但是添加一个name字段，就不能去重了，因name字段不重复
select distinct name,english from student;
```

### 3.4.2进阶用法

#### 表达式

```sql
#select语句可以使用表达式对查询的结果进行运算
select　*|{col1 | expr,col2 | expr...}
	from tab_name;
#在select语句中可使用as语句
select col_name as 别名 from tab_name;
#别名在某些情况下十分有用，有时候甚至必须取别名
--别名前面也可以不加as
--别名如果是多个单词组成中间有空格，那么必须用引号包裹起来，否则会认为别名是前一个单词，后面那个单词就成了语法错误了
```

练习

```sql
#统计每个学生的总分
select name,(chinese+english+math) from student;

#在所有学生总分基础上加10分
select name,(chinese+english+math)+10 from student;

#使用别名表示学生分数
select name,(chinese+english+math) as total_score from student;
```

#### where

只要是带有过滤性质的查询，都需要使用where。

在where子句中经常使用的运算符

![image-20210508144824675](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210508144824675.png)

练习

```sql
--查询姓名为比克的学生成绩
select * from student where name = "比克"; 
--查询英语成绩大于90分的同学
select * from student where english > 90; 
--查询总分大于200分的所有同学
select * from student where (chinese+ math+ english) > 200; 
--查询math大于60 并且 id 大于3的学生成绩
select * from student where math > 60 and id >3;
--查询英语成绩大于语文成绩的同学
select * from student where english >chinese;
--查询总分大于200分 并且数学成绩小于语文成绩，姓孙的学生
--"孙%" 表示名字以 "孙" 开头的就可以
select * from student 
	where (chinese + math +english)>200 
	and math < chinese
	and name like "孙%";
--查询英语成绩在 80~90 之间的同学，between...and...是一个闭区间
select * from student where english between 80 and 90;
--查询数学成绩为 89,90,91的同学
select * from student where math in (89,90,91);
--查询数学分数>80，语文分数>80的同学
select * from student where math >80 and chinese >80;
```

#### order by

当需要对查询结果进行排序的时候，可以结合order by语句来实现。

```sql
#使用order by 子句排序查询结果
select col1 ,col2,col3..
	from table;
	order by column asc | desc
```

说明：

- order by指定排序的列，排序的列既可以是表中的列名，也可以是select语句后指定的列名
- asc 指升序排列（默认），desc指降序排列
- order by子句应位于select语句的结尾

练习

```sql
--对数学成绩排序后输出（升序）
select name,math from student order by math; 
--对总分按从高到低的顺序输出
select name,(math+chinese+english) as total_score 
	from student 
	order by total_score desc; 
--对姓孙的学生成绩排序输出（升序）
select name,(math+chinese+english) as total_score
	from student
	where name like "孙%"
	order by total_score;
```

## 四、函数

select通常和函数结合使用，提高查找效率。

## 4.1统计函数

#### 4.1.1- count

```sql
--count返回行的总数
select count (*) | count(col_name) from table_name
	[where where_definition]
```

练习

```sql
--统计一个班级共有多少个学生？
select count(*) from student;
--统计数学成绩大于90的学生有多少个？
select count(*) from student
	where math >85;
--统计总分大于250的人数有多少？
select count(*) from student 
	where (chinese + english + math) >250;
--count(*)和count(列)的区别
--count(*)返回满足条件的记录的行数
--count(col_name)统计满足条件的某列有多少个，但是会排除为空（null）的情况
create table t15(
	name varchar(10));
insert into t15 values("弗利萨");
insert into t15 values("沙鲁");
insert into t15 values("魔人布欧");
insert into t15 values(null);
--t15有4条记录，其中一条为空null
select count(*) from t15;	#返回4，因为一共有4条记录
select count(name) from t15;	#返回3，排除了null这条空记录
```

#### 4.1.2- sum

sum函数返回满足where条件的行记录的和，一般使用在数值列

```sql
select sum(col_name) {,sum (col_name)...} from tab_name
	[where where_definition]
```

l练习

```sql
--统计一个班级数学总成绩？
select sum(math) from student;
--统计一个班级语文、英语、数学各科的总成绩
select sum(chinese),sum(math),sum(english) from student;
--统计一个班级语文、英语、数学的成绩总和
select sum(chinese+math+english) 
	as total_score 
	from student;
--统计一个班级语文成绩平均分
select sum(chinese)/count(*) as average_chinese from student;
--注意：sum仅对数值起作用，否则会报错
--注意：对多列求和，“,”号不能少
```

#### 4.1.3-avg

avg比较简单，下面看个练习

```sql
--求一个班级数学平均分？
select avg(math) from student;
--求一个班级总分平均分
select avg(math+chinese+english) from student;

```

#### 4.1.4-max/min

max/min函数返回满足where条件的一列的最大/最小值

```sql
select max(列名) from tab_name
	[where where_definition]
```

练习

```sql
--求班级最高分和最低分（数值范围在统计中特别有用）
--怎么求出最高分和最低分是谁，后面再解释
select 	max(math+chinese+english)as `max`,
	   min(math+chinese+english)as `min` 
	   from student;
--求出班级数学最高分和最低分
select 	max(math)as `max_math`,
	   min(math)as `min_math` 
	   from student;
--随着业务需求不断复杂，写出来的sql语句也会越来越复杂
```

#### 4.1.5-group by

group by子句用于对列进行分组

```sql
select col1 ,col2 ,col3...
	from table
	group by column1,column2...;
--使用having子句对分组后的结果进行过滤
select col1 ,col2 ,col3...
	from table
	group by column having...;
```

说明：

group by用于对查询的结果分组统计，

having子句用于限制分组显示结果。

![image-20210508181542810](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210508181542810.png)

练习

```sql
--创建测试表
--部门表
create table dept( /*部门表*/
deptno mediumint unsigned  not null default 0, #部门编号
dname varchar(20)  not null default "", #部门名称
loc varchar(13) not null default ""	#部门所在地
);

insert into dept values(10, 'ACCOUNTING', 'NEW YORK'), (20, 'RESEARCH', 'DALLAS'), (30, 'SALES', 'CHICAGO'), (40, 'OPERATIONS', 'BOSTON');
--员工表
CREATE TABLE emp
(empno  MEDIUMINT UNSIGNED  NOT NULL  DEFAULT 0, /*编号*/
ename VARCHAR(20) NOT NULL DEFAULT "", /*名字*/
job VARCHAR(9) NOT NULL DEFAULT "",/*工作*/
mgr MEDIUMINT UNSIGNED ,/*上级编号*/
hiredate DATE NOT NULL,/*入职时间*/
sal DECIMAL(7,2)  NOT NULL,/*薪水*/
comm DECIMAL(7,2) ,/*红利*/
deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0 /*部门编号*/
);
 INSERT INTO emp VALUES(7369, 'SMITH', 'CLERK', 7902, '1990-12-17', 800.00,NULL , 20), 
(7499, 'ALLEN', 'SALESMAN', 7698, '1991-2-20', 1600.00, 300.00, 30),  
(7521, 'WARD', 'SALESMAN', 7698, '1991-2-22', 1250.00, 500.00, 30),  
(7566, 'JONES', 'MANAGER', 7839, '1991-4-2', 2975.00,NULL,20),  
(7654, 'MARTIN', 'SALESMAN', 7698, '1991-9-28',1250.00,1400.00,30),  
(7698, 'BLAKE','MANAGER', 7839,'1991-5-1', 2850.00,NULL,30),  
(7782, 'CLARK','MANAGER', 7839, '1991-6-9',2450.00,NULL,10),  
(7788, 'SCOTT','ANALYST',7566, '1997-4-19',3000.00,NULL,20),  
(7839, 'KING','PRESIDENT',NULL,'1991-11-17',5000.00,NULL,10),  
(7844, 'TURNER', 'SALESMAN',7698, '1991-9-8', 1500.00, NULL,30),  
(7900, 'JAMES','CLERK',7698, '1991-12-3',950.00,NULL,30),  
(7902, 'FORD', 'ANALYST',7566,'1991-12-3',3000.00, NULL,20),  
(7934,'MILLER','CLERK',7782,'1992-1-23', 1300.00, NULL,10);
--工资级别表
CREATE TABLE salgrade
(
grade MEDIUMINT UNSIGNED NOT NULL DEFAULT 0, #工资级别
losal DECIMAL(17,2)  NOT NULL,	#该级别的最低工资
hisal DECIMAL(17,2)  NOT NULL	/*该级别的最高工资*/
);

INSERT INTO salgrade VALUES (1,700,1200); #插入记录
INSERT INTO salgrade VALUES (2,1201,1400);
INSERT INTO salgrade VALUES (3,1401,2000);
INSERT INTO salgrade VALUES (4,2001,3000);
INSERT INTO salgrade VALUES (5,3001,9999);
--如何显示每个部门的平均工资和最高工资
--分析：avg(sal),max(sal) 按照部门来分组查询
select avg(sal),max(sal) from emp
	group by deptno; #40号部门没人，故未显示
--显示每个部门对的每种岗位的平均工资和最低工资
--写sql语句的思路：化繁为简，各个击破
--分析：1.每个部门的平均和最低工资 2.每个部门的每种岗位的平均和最低工资
select avg(sal),min(sal) from emp
	group by deptno ,job;
	
--显示平均工资低于2000的部门号和它的平均工资（别名）
--分析：1.显示各个部门的平均工资和部门号 
--	   2.在之前基础之上进行过滤，保留avg(sal) < 2000
select deptno,avg(sal) as sal_avg from emp
	group by deptno
		having sal_avg < 2000 ;
```

## 4.2字符串函数

![image-20210508182042055](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210508182042055.png)

练习

```sql
--使用emp表来演示字符串和相关函数的使用
--charset(str)返回字符串字符集
select charset(ename) from emp;
--concat(string2[,...]) 连接字符串，将多个列拼接成1列
select concat(ename,'的工作是',job) from emp;
--instr(string,substring)返回substring在string中出现的位置，没有返回0
select instr('abcdefg','cde') from dual;
--注：dual(亚元表，系统表)，mysql自带的一张表，无表可用时可用这张表做测试
--ucase(string) 将字符串转换成大写
select ucase(ename) from emp;
--lcase(string) 将字符串转换成小写
select lcase(ename) from emp;
--left(string,length) 从string中的左边起，取length个字符，右边就是right
select left(ename,2) from emp;
select right(ename,2) from emp;
--length(string) 求string的长度（按字节）
select length("刘书鑫") from dual;
--replace(str ,search_str,replace_str) 在str中用replace_str替换search_str
--；例如：如果是manager，就替换成"经理"
select replace(job,ucase("manager"),"经理") from emp;
--strcmp(str1,str2) 逐字符比较两字串大小
select strcmp("1234","1235") from dual;	#返回-1
select strcmp("1234","1234") from dual;	#返回0
--substring(str,position [,length]) 从str的position开始[从1开始计算]，取length个字符
select substring(ename,1,2) from emp;
--ltrim(string) rtrim(string) trim 去除前端空格或后端空格 
select ltrim("   1234") from dual;
```

课后练习

以首字母小写的方式显示所有员工emp表的姓名。

```sql
#方法1：
select concat(lcase(left(ename,1)),right(ename,length(ename)-1))
	as new_name
	from emp;
#方法2：
select concat(lcase(substring(ename,1,1)),substring(ename,2))
	as new_name
	from emp;

```

## 4.3数学函数

![image-20210509105652150](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210509105652150.png)

练习

```sql
--abs(num) 取绝对值
select abs(-10) from dual;
--bin(decimal_num)
select bin(10) from dual;
--ceiling(num) 向上取整，得到比num大的最小整数
select ceiling(3.4) from dual;
--conv(num,from_base,to base) 进制转换
--下面这条语句的意思是将10进制的8，转换为2进制的8
select conv(8,10,2) from dual;
--floor(num) 向下取整，得到比num小的最大整数
select floor(-1.6) from dual;
--fromat(num ,decimal_places)
--这个函数很重要，可以用来保留小数位数
--查询的时候，如果小数位数很多，就可以用这条指令处理。
select format(78.13243556,2) from dual; #按四舍五入保留小数位数
--hex(decimai_num) 转16进制
select hex(12) from dual;
--least(num,num2 [,...]) 求最小值
select least(15,2,78,3,1,59) from dual;
--mod(numerator,denominator) 求余
select mod(8,3) from dual;
--rand([seed]) 其范围为0<= v <= 1.0 v是随机生成的数
--一旦给定一个seed种子，那么每次生成的随机数都是固定的
select rand() from dual;
```

## 4.4时间日期相关函数

![image-20210509112230976](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210509112230976.png)

练习

```sql
--一、current_date()、current_time()、current_timestamp()
--current_date()
select current_date() from dual;
select current_date from dual; # 带不带括号都可以
--current_time()
select current_time() from dual;
--current_timestamp()
select current_timestamp() from dual;
--now()
select now() from dual;

--创建测试表 信息表
create table mes(
	id int,
	content varchar(30),
	sent_time datetime);
--添加一条记录
--重复执行下面这条指令，每次添加的记录时间戳都是不一样的
insert into mes
	values(1,"北京新闻",current_timestamp());
select * from mes;
--添加其他记录
insert into mes
	values(2,"郑州新闻",now());
insert into mes
	values(3,"包子山新闻",now());
insert into mes
	values(4,"春日部新闻",now());
--二、date_add、date_sub、datediff
--查询：
--1.显示所有留言信息，发布日期只显示日期，不用显示时间
select content , date(sent_time) as sent_date
	from mes;
--2.查询在10分钟内发布的帖子
--商城项目查询订单常用
select content from mes
	where date_add(sent_time,interval 10 minute) >= now();
--3.在mysql的sql语句中求出2011-11-11 和 1990-1-1相差多少天
select datediff('2011-11-11','1990-01-01') from dual;
--4.用mysql的sql语句求出你活了多少天（练习）
--5.如果你能活80岁，求出你还能活多少天（练习）

--三、yesr | month | day | date(datetime)
select year(now()) from dual;
select month(now()) from dual;
select day(now()) from dual;
select year('2013-11-10') from dual;

--四、unix_timestamp() 返回1970-1-1到现在的毫秒数
select unix_timestamp()/(24*3600*365) from dual;
--from_unixtime()可以把一个unix_timestamp秒数，转成指定格式的日期
--意味着在实际开发中，可以存放一个整数，表示时间，通过from_unixtime转换
--例如，看到表中的时间列类型是整数也不要惊讶，因为可能通过这个函数转换成时间
select from_unixtime(1618483484,'%Y-%m-%d') from dual;
select from_unixtime(1618483484,'%Y-%m-%d %H:%i:%s') from dual;


```

细节补充

- date_add()中的interval后面可以是 year hour minute second day等
- date_sub()中的interval后面可以是 year hour minute second day等
- datediff(date1,date2)得到的是天数，而且是date1-date2的天数，因此可以取负数
- 这四个函数的日期类型可以是date，datetime或者timestamp

## 4.5加密函数

![image-20210509145203495](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210509145203495.png)

练习

```sql
--演示加密函数和系统函数
--user() 可以查看登陆到mysql的有哪些用户，以及登陆的ip，包括远程和本地登陆
select user() from dual; --用户@IP地址
--database()查询当前使用的数据库名称
select database();
--md5(str)
--tips:md5已经被一个武汉的教授攻破了
--root 密码是lsx —> 加密md5 -> 在数据库中存放的是加密后的密码
select md5('lsx') from dual;
select length(md5('lsx')) from dual;

--创建演示用的用户表，存放密码时，是md5
create table users(
	id int,
	name varchar(32) not null default '',
	pwd char(32) not null default '');
insert into users 
	values(1,'刘书鑫',md5('lsx')); --一定要存放密文，明文容易被泄露
select * from users;

select * from users  --sql注入问题
	where `name` = '刘书鑫' and pwd = md5('lsx');

--password(str) 也是一个加密函数，使用的是另外的加密算法
--mysql数据库的用户密码就是password函数加密的
select password('lsx') from dual;

--select * from mysql.user \G 从原文密码str 计算并返回密码字符串
--通常用于对mysql数据库的用户密码加密
--mysql.user 表示 数据库.表
select * from mysql.user;

```

补充知识：

除了我们人为创建的数据库，mysql还自带了一些数据库，比如mysql库，我们的用户名和密码都保存在这个库中的user表里。

## 4.6流程控制函数

先看两个需求：

1. 查询emp表，如果comm是null，则显示0.0
2. 如果emp表的job是clear，则显示职员，如果是manager则显示经理，如果是salesman则显示销售人员，其他正常显示

显然，上述需求需要借助流程控制函数来实现

![image-20210509152130440](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210509152130440.png)

```sql
--操作流程控制语句
--if(expr1,expr2,expr3)
select if(false,'七龙珠','海贼王') from dual;
--ifnull(expr1,expr2) 如果expr1不为空null,则返回expr1，否则返回expr2
select ifnull('七龙珠','海贼王') from dual;
--select case when expr1 then expr2 when expr3 then expr4 else expr5 end;
--类似于多重分支
select case 
	when false then "孙悟空"
	when true then "贝吉塔"
	else "特南克斯"
	end;

--解题
--需求1：
--说明：判断是否为空，要使用is null，判断不为空，使用 is not
--方法一：
select  ename,if(comm is null , 0.0,comm)
	from emp;
--方法二：
select  ename,ifnull(comm, 0.0)
	from emp;
	
--需求2：
select ename,(select case
             when job = 'CLERK' then '职员'
             when job = 'SALESMAN' then '销售人员'
             when job = 'MANAGER' then '经理'
             else job end) as job
 	from emp;
	
```

以上是常用的流程控制语句，当然mysql还有其他的流程控制语句，用到的时候翻手册即可。

# ===================

以上都是基于单表查询的select语句，实际开发中，多表查询用的更多。下面就进入多表查询。

# 五、mysql表查询-加强

在前面我们讲过mysql表的基本查询，但都是针对一张表进行的查询，这在实际的软件开发中，还远远不够。

下面我们演示如何进行多表查询，使用前面创建的三张表（emp，dept，salgrade）。

## 5.1单表加强

首先我们对之前学习的单表查询做一个加强

### 5.1.1子句

```sql
--使用where子句
--如何查找1992.1.1后入职的员工？
--mysql中的日期类型也可以参与比较
select * from emp
	where hiredate >'1992-1-1'; --这里的日期格式要按照表中的格式进行
--如何使用like操作符（模糊查询）
--	%-表示0到多个任意字符 _-表示单个任意字符
--	如何显示首字符为s的员工姓名和工资？
select ename,sal from emp
	where ename like "S%";
--	如何显示第三个字符为大写O的所有员工的姓名和工资
select ename,sal from emp
	where ename like "__O%";
--如何显示没有上级的雇员的情况
select * from emp
	where mgr is null;
--查询表结构
desc emp;

--使用order by子句
--如何按照工资的从低到高的顺序，显示雇员的信息？
select * from emp
	order by sal;
--如何按照部门号升序而雇员的工资降序排列，显示雇员信息？
select * from emp
	order by deptno asc,sal desc;
```

### 5.1.2分页查询（重要，一定会用到！）

实际开发中，往往不可能将内容全部存放在一张表上展示出来。这个时候就需要用到分页查询。

mysql中的分页查询是做的最简单的。

```sql
--分页查询
--1.按雇员的id号升序取出，每页显示3条记录，请分别显示第1页，第2页，第3页
--2.基本语法 select...limit start, rows 表示从start+1行开始取，取出rows行，start从0开始计算
--第1页
select * from emp
	order by empno
	limit 0 ,3;
--第2页
select * from emp
	order by empno
	limit 3 ,3;
--第3页
select * from emp
	order by empno
	limit  6,3;
--3.推导一个公式：
--select * from emp
--	order by empno
--	limit 每页显示记录数* （第n页-1）, 每页显示记录数

```

### 5.1.3分组加强

```sql
--显示每种岗位的雇员总数、平均工资
select count(*) ,avg(sal),job
	from emp
	group by job;
--显示雇员总数，以及获得补助的雇员数(comm非空，说明获得补助)
--count(列)会自动排除为空的情况
select count(*) ,count(comm)
	from emp;
--统计没有获得补助的雇员数
--先判断列是否为空，是空就返回1，不为空返回null值
--利用count不统计空值的特点，来统计没有获得补助的雇员数
select count(*) ,count(if(comm is null,1,null))
	from emp;
--sql语句是很灵活的，上述要求还可以这样来实现：
select count(*),count(*) -count(comm ) as comm_null
	from emp;

--显示管理者的总人数
--要排除重复统计的情况
select count(distinct mgr) from emp;

--显示雇员工资的最大差额
select max(sal) - min(sal)
	from emp;
```

 总结

如果select语句同时含有group by，having，limit，order by 那么他们的顺序是group by，having，order by ，limit

```sql
--顺序写错的话，语法通不过
select col1,col2 ... from table
	group by col	--分组
	having condition	--分组过滤
	order by col	--分组排序
	limit start,rows;	--分组分页显示

----应用案例：统计各个部门的平均工资，并且是大于1000的，并且按照平均工资从高到低排序，取出前两行记录
select deptno,avg(sal) as avg_sal from emp
	group by deptno
		having avg_sal > 1000
    order by avg_sal desc
    limit 0,2;
```

## 5.2多表查询

问题引出

以商城为例，假如某个评论的过程中附加了商品的链接，那么评论表和商品表就联合起来了。

多表查询是指基于两个和两个以上的表查询。在实际应用中，查询单个表可能不能满足需求（如下面的课堂练习），需要使用到（dept表和emp表）。

```sql
--多表查询练习
--1.显示雇员名称，雇员工资及所在部门的名字（笛卡尔集）
--雇员名称和工资来源于emp表，部门名称来自dept表
--需要对表的结构有一个清晰的认识
--小技巧：多表查询的条件不能少于表的个数-1，否则会出现笛卡尔集
--例如，这样来查询：
select * from emp,dept;
--这样来查询的话，会在第一张表中取出每一行，和第二张表的每一行进行组合，返回结果
--一共返回的记录数就是（表1行数 * 表二的行数）
--这样的多表查询默认处理返回的结果，称为笛卡尔集
--解决这个多表的关键，就是要写出正确的过滤条件 where
--这个where 条件往往是需要分析的，不是那么明显的
select ename,sal,dname 
	from emp,dept
	where emp.deptno = dept.deptno; 
--注意，两张表都含有deptno,所以要指定是哪张表的deptno，否则会因为指定不明确而报错

--2.如何显示部门号为10的部门名、员工名和工资
select dname,ename,sal
	from emp,dept
	where 
	emp.deptno = dept.deptno 
	and emp.deptno = 10;
--3.显示各个员工的姓名、工资及其工资的级别
--写sql，都是先写一个比较简单的，然后加上过滤条件
select ename , sal ,grade
	from emp,salgrade
	where sal between losal and hisal;

--练习：显示雇员姓名、雇员工资及所在部门的名字，并按照部门排序（降序排）
```

### 5.2.1自连接

自连接是指对同一张表的连接查询（将同一张表看作两张表）

```sql
--显示公司员工和他的上级的名字
--分析员工名字在emp，上级的名字也在emp
--员工和上级使用过emp的mgr列关联的
--同一张表必须取别名，否则会报错
select worker.ename as "职员名",boss.ename as "上级名"
	from emp worker,emp boss 
	where worker.mgr = boss.empno;
```

小结：

1. 把同一张表当作两张表使用
2. 需要给表取别名（必须，否则会报错）： 表名 表别名
3. 如果列名不明确，可以指定列的别名（建议）： 表别名.列名 as 列别名

### 5.2.2mysql子查询

子查询是指嵌入在其他sql语句中的select语句，也叫嵌套查询

#### 单行子查询

```sql
--如何显示与smith同一部门的所有员工？
--首先要查询到Smith的部门编号
select deptno from emp
	where ename = 'SMITH';
--接着把上述sql语句当作子查询
select * 
	from emp
	where deptno = (
    	select deptno from emp
    		where ename = 'SMITH'
    );
--因为子查询返回结果只有一行，所以也称单行子查询
```

#### 多行子查询

```sql
--如何查询和部门10的工作相同的雇员的
--名字、岗位、工资、部门号，但是不包含10号部门自己的雇员
--首先查询到10号部门有那些工作
--这里记得去重
select distinct job
	from emp
	where deptno = 10;
--把上面查询的结果当作子查询使用
select ename ,job,sal,deptno
	from emp
	where job in (
    	select distinct job
            from emp
            where deptno = 10
    ) and deptno != 10;
--mysql中相等就是单等号"="，不等就是"<>"或者"!=" 
```

all和any操作符

这两个操作符理解即可。

```sql
--如何显示工资比部门30的所有员工的工资高的员工的姓名、工资和部门号
select ename,sal,deptno
	from emp 
	where sal > all(
    	select sal
    		from emp
    		where deptno = 30
    ); --这里的all 换成max效果也是一样的
--如何显示工资比部门30号的其中一个员工工资高的员工的姓名、工资和部门号
--比30号部门其中一个员工高就行
select ename,sal,deptno
	from emp 
	where sal > any(
    	select sal
    		from emp
    		where deptno = 30
    );--这里的any 换成min效果也是一样的

```

#### 多列子查询

多列子查询是指查询返回多个列数据的子查询语句

(字段1，字段2...) = （select 字段1，字段2 from...）

```sql
--思考如何查询与allen的部门和岗位完全相同的所有雇员（并且不含Smith本人）
--分析：
--首先我们要得到allen的部门和岗位
select deptno , job
	from emp
	where ename = 'ALLEN';
--把上面的查询当作子查询来使用，并且使用多列子查询的语法进行匹配
select *
	from emp
	where (deptno , job) = (
    	select deptno , job
		from emp
		where ename = 'ALLEN'
    ) and ename != 'ALLEN';
   
--练习：请查询和孙悟空数学，英语，语文成绩完全相同的学生   
--添加一条记录，使得存在查询结果
insert into student
	values(8,"吉连",89,78,90);
--首先我们获取孙悟空数学，英语，语文成绩
select math,english,chinese
	from student
	where name = "孙悟空";
--把上述查询结果当作子查询来使用
select id,name 
	from student
	where (math,english,chinese) = (
    	select math,english,chinese
		from student
		where name = "孙悟空"
    ) and  name <> "孙悟空";
```



#### 其他用法

以上都是子查询应用在where子句中作为筛选条件的，

其实子查询也可以在from子句中使用，当作临时表来查询

```sql
--案例1：查询ecshop中各个类别里面价格最高的商品
--临时表是不存在的，但是可以把它当作一张表来使用
--先查询一下 商品表的内容
select * from ecs_goods;
--得到各个类别中，价格最高的商品 max + group by cat_id，把这个表当作临时表
select cat_id ,max(shop_price)
	from ecs_goods
	group by cat_id;
select goods_id,cat_id,goods_name,shop_price
	from ecs_goods;
--接着我们要先匹配cat_id,找到商品类，然后在商品类里面找到价格最高的商品
select goods_id,ecs_goods.cat_id,goods_name,shop_price
	from (
    	select cat_id ,max(shop_price) as max_price
		from ecs_goods
		group by cat_id
    ) temp ,ecs_goods
    where temp.cat_id = ecs_goods.cat_id
    	  and  temp.max_price = ecs_goods.shop_price;
    	 
--案例2：查找每个部门工资高于本部门平均工资的人的资料
--首先得到每个部门的部门号和对应的平均工资
select deptno ,avg(sal)
	from emp
	group by deptno;
--把上面的结果当作子查询，和emp表进行多表查询
--
select ename,sal,temp.avg_sal,emp.deptno
	from emp,(
    	select deptno ,avg(sal) as avg_sal
		from emp
		group by deptno
    ) temp
    where emp.deptno = temp.deptno and emp.sal >temp.avg_sal;
    
--案例3：查找每个部门工资最高的人的详细资料
--首先得到每个部门和本部门的最高工资
select deptno,max(sal) from emp
	group by deptno;
--把上述查询结果当作一个子查询，和emp表进行多表查询
select ename,temp.deptno,sal
	from emp,(
    	select deptno,max(sal) as max_sal from emp
		group by deptno
    ) temp
    where emp.deptno = temp.deptno 
    and sal = max_sal;

--案例4：查询每个部门的信息（包括：部门号，编号，地址）和人员数量
--部门名、编号、地址来自dept表，
--各个部门的人员数量是没有的，可以构建一张临时表来查询
select count(*) ,deptno
	from emp
	group by deptno;
--把上述表当作临时表来查询
select dname,dept.deptno,loc,per_num as "人数"
	from dept,(
    	select count(*) as per_num ,deptno
		from emp
		group by deptno
    )tmp
    where tmp.deptno = dept.deptno;
--表.* 表示将该表所有列都显示出来，可以简化sql语句
--在多表查询中，当多个表的列不重复时，才可以直接写列名，否则要加前缀 表名.列
--故上述查询还可以这样写：
select tmp.*,dname,loc
	from dept,(
    	select count(*) as per_num ,deptno
		from emp
		group by deptno
    )tmp
    where tmp.deptno = dept.deptno;
```

把子查询当作一张临时表，可以解决很多复杂的查询。

查询效率的优化以后再讨论。

## 5.3表复制

- 自我复制数据（蠕虫复制）

  有时候，为了对某个sql语句进行效率测试，我们需要海量数据时，可以使用此法为表创建海量数据。

  ```sql
  --表的复制
  --先创建一张表
  create table test(
  	id int,
  	name varchar(20),
      sal double,
      job varchar(32),
      deptno int
  );
  --演示如何自我复制
  --1.先把emp表的记录复制到test
  insert into test
  	(id,name,sal,job,deptno)
  	select empno,ename,sal,job,deptno from emp;
  --2.自我复制
  insert into test
  	select * from test;
  --自我复制可以1变2,2变4,4变8等等，通常用来测试sql语句的效率
  
  --经典面试题
  --如何删除一张表的重复记录？
  --1.先创建一张表 test2
  --2.让test2有重复的记录
  create table test2 like emp;
  --上述语句把emp表的结构（列），复制到test2
  --添加数据
  insert into test2
      select * from emp;
  --自我复制
  insert into test2
  	select * from test2;
  --此时我们已经构建了一张拥有重复记录的表
  --3.考虑去重(方法不唯一，这里使用个常见的方法)
  --思路：（1）先创建一张临时表 my_tmp,该表的结构和test2一样
  create table my_tmp like test2;
  --（2）把my_tmp的记录 通过distinct关键字 处理后 把记录复制到 my_tmp
  --其实此时把这个临时表改个名就可以了
  insert into my_tmp
  	select distinct * from test2;
  --(3)清除掉 test2 记录
  delete from test2;
  --（4）把my_tmp表的记录复制到test2
  insert into test2 
  	select * from my_tmp;
  --（5）drop掉临时表my_tmp
  drop table my_tmp;
  
  ```

## 5.4合并查询

有时在实际应用中，为了合并多个select语句的结果，可以使用集合操作符号：

union，union all

```sql
--union all
--该操作符用于取得两个结果集的并集，当使用该操作符时，不回取消重复行
select ename ,sal,job from emp
	where sal > 2500; -- 会查询到5条结果
select ename ,sal,job from emp
	where job = 'MANAGER'; --会查询到3条结果
--union all 就是将两个查询结果合并，不会去重
select ename ,sal,job from emp
	where sal > 2500
union all
select ename ,sal,job from emp
	where job = 'MANAGER';
 --union可以将两个查询结果合并，并且有重复记录的话会去重
 select ename ,sal,job from emp
	where sal > 2500
union
select ename ,sal,job from emp
	where job = 'MANAGER';
```

# 六、mysql外连接

问题引出：

前面我们学习的查询，是利用where子句对两张表或者多张表形成的笛卡尔集进行筛选，根据关联条件，显示所有匹配的记录，匹配不上的，不显示

比如：列出部门名称和这些部门的员工名称和工作，同时要求显示出那些没有员工的部门。

使用我们学习过的多表查询的sql，看看效果如何？

```sql
select dname,ename,job
	from emp,dept
	where emp.deptno = dept.deptno
	order by dname;
--上述查询存在一个问题，由于40号部门没有员工，所以两张表无法根据emp.deptno = dept.deptno匹配显示
```

外连接：

1. 左外连接：如果左侧的表完全显示（即使左右表没有匹配，左表也会完全显示），我们就说是左外连接；

   `select ... from tab_1 left join tab_2 on 条件`

   tab_1就是左表，tab_2就是 右表

2. 右外连接：如果右侧的表完全显示（即使左右表没有匹配，右表也会完全显示），我们就说是右外连接。

   `select ... from tab_1 right join tab_2 on 条件`

   tab_1就是左表，tab_2就是 右表

```sql
--创建stu
create table stu(
	id int,
    name varchar(20)
);
--插入4条记录
insert into stu values(1,'jack'),(2,"tom"),(3,"kity"),(4,"nono");
--创建exam
create table exam(
	id int,
	grade int);
--插入3条记录
insert into exam values(1,56),(2,76),(11,8);
--使用左连接，显示所有人的成绩，如果没有成绩，也要显示该人的姓名和id号，成绩显示为空
select name ,stu.id,grade
	from stu left join exam
	on stu.id = exam.id;
--使用右外连接，显示所有成绩，如果没有名字匹配，显示为空
--即右边的表exam和左表没有匹配的记录，也会把右表的记录显示出来
select name ,stu.id,grade
	from stu right join exam
	on stu.id = exam.id;
	
--小练习：
--列出部门名称和这些部门的员工信息（名字和工作），同时列出那些没有员工的部门名。 
--1.使用左外连接实现
select dname ,ename,job
	from dept left join emp
	on emp.deptno = dept.deptno;
--2.使用右外连接实现
select dname ,ename,job
	from emp right join dept
	on emp.deptno = dept.deptno;
```

小结：

在实际开发中，我们绝大多数情况下使用的是前面学过的内连接。

# 七、mysql约束

## 7.1primary key(主键)

```sql
--基本用法
字段名 字段类型 primary key
--用于唯一的标示表行的数据，当定义主键约束后，该列不能重复

--使用案例
create table t17(id int primary key,
                name varchar(20),
                 email varchar(20)
                );
--上述操作表示id列是主键，主键列的值是非空的，且不能重复的
insert into t17
	values(1,'孙悟空','sunwukong@qq.com');
insert into t17
	values(2,'贝吉塔','beijita@qq.com');	
insert into t17
	values(1,'比克','bike@qq.com'); --会报错，因为主键值不能重复

--复合主键
create table t16(id int primary key,
                name varchar(20) primary key,
                 email varchar(20)
                );--会报错，因为不可以直接将两列都指定为主键
--但可以这样来将 id + name 指定为复合主键
create table t16(id int,
                name varchar(20),
                 email varchar(20),
                 primary key(id,name)
                );
--上述就指定了（id和name）为复合主键，
--添加数据的时候，只有id和name同时相同，才违背了复合主键的规则
insert into t16
	values(1,'孙悟空','sunwukong@qq.com');  
insert into t16
	values(1,'贝吉塔','beijita@qq.com'); --可以添加成功，与上一条记录只有id相同，name不同
insert into t16
	values(1,'孙悟空','bike@qq.com'); --添加失败，复合主键相同
```

细节补充：

- primary key不能重复而且不能为null
- 一张表最多只能有一个主键，但可以是复合主键
- 主键的指定方式有两种：
  1. 直接在字段名后指定：字段名 primary key
  2. 在表定义最后写primary key(列名)
- 使用desc 表名，可以看到primary key 的情况
- 在实际开发中，每个表往往都会设计一个主键

## 7.2not null(非空)

如果在列上定义了not null，那么当插入数据时，必须为列提供数据，不能为空。

```sql
--字段名 字段类型 not null
```

## 7.3unique(唯一)

```sql
--当定义了唯一约束后，该列值是不能重复的
--字段名 字段类型 unique
create table t18(id int unique,
                name varchar(20),
                 email varchar(20)
                );
--上述创建表的过程中，id字段是unique唯一的
insert into t18
	values(1,'孙悟空','sunwukong@qq.com');  
insert into t18
	values(1,'贝吉塔','beijita@qq.com');
--如果未指定非空约束，unique列可以指定多个null
insert into t18
	values(null,'孙悟空','sunwukong@qq.com');  
insert into t18
	values(null,'孙悟空','sunwukong@qq.com');  --可以正常添加
```

细节补充：

1. 如果没有指定not null，则unique字段可以有多个null
2. 一张表可以有多个unique字段

## 7.4foreign key(外键)

用于定义主表和从表之间的关系：

1. 当外键约束要定义在从表上，主表则必须具有主键约束或是unique约束，这样这个列才能是唯一的，否则从表对应过来的时候，不知道对应的具体是哪条记录

2. 当定义外键约束后，要求外键列数据必须在主表的主键列存在或是为null

   (学生/班级 图示)

![image-20210511144150262](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210511144150262.png)

如上图中，我们把class_id做成外键约束，那么hsp这个同学就无法正常添加，因为他的班级编号在主表中不存在。

反过来，如果外键约束已经形成，例如jack的班级编号跟主表的id列形成约束了，那么直接删除主表的id字段也会失败。

```sql
--foreign key(本表字段名) references 主表名(主键名或unique字段名)
-- 学生/班级 外键演示
--先创建主表，因为外键表要用主表id
create table class(
	id int primary key,
    name varchar(20) not null default ''
);
--创建从表stu1
create table stu1(
	id int primary key,
    name varchar(20) not null default '',
    class_id int,
    foreign key(class_id) references class(id)
);
--上述语句中，class_id就相当于是外键
--测试数据
insert into class
	values(100,"java"),(200,"web");
select * from class;
insert into stu1
	values(1,"tom",100);
insert into stu1
	values(2,"jack",200);
insert into stu1
	values(3,"John",300);--故意添加一个不存在的id，会添加失败
insert into stu1
	values(3,"John",null);--加空是可以的，例如暂时不知道该学生班级，前提唯一约束，没有非空约束
```

细节补充：

1. 外键指向的表的字段，要求是primary key或者是unique
2. 标的类型是innodb，这样的表才支持外键
3. 外键字段的类型要和主键字段的类型一致（长度可以不同）
4. 外键字段的值，必须在主键字段中出现过，或者为null（前提是外键字段允许为null）
5. 一旦建立主外键的关系，数据就不能随意删除了

## 7.5check

用于强制行数据必须满足的条件，假定在sal列上定义了check约束，并要求sal列值在1000~2000之间如果不在1000~2000之间就会提示出错。

Oracle和sql server均支持check，但是mysql5.7目前还不支持check，只做语法校验，也就是说，你可以写上去check，但它不会生效。

```sql
--基本语法
列名 类型 check (check条件)
user 表
id ,name ,sex(man,woman),sal(大于100 小于900)
--案例演示
create table t19(
	id int,
    name varchar(20),
    sex varchar(6) check (sex in("man","woman")),
    sal double check (sal > 1000 and sal < 2000)
);
--添加数据
insert into t19
	values(1,'jack','男',1);--虽然不在check约束范围内，但也能添加成功
--8以上版本的check已经生效了
```

在mysql中实现check的功能，一般是在程序中控制，或者通过触发器完成的。

## 7.6约束练习

![image-20210511151252438](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210511151252438.png)

```sql
--建表，其实这就是一个简单常见的销售数据表
create table goods(
	goods_id int primary key,
    goods_name varchar(20) not null default '',
    unitprice decimal(10,2) not null default 0 check (1.0 <= unitprice and  unitprice <= 9999.99) ,
    category int not null default 0,
    provider varchar(20) not null default ''
);
--字段类型都是程序员根据实际需要，自己定义的
--sex也可以用枚举类型来设定：
--sex enum('男','女') not null 这种形式是生效的
create table customer(
	customer_id int primary key,
    name varchar(20) not null default '',
    address varchar(32),
    email varchar(20) unique,
    sex varchar(1) check (sex in ("男","女")),
    card_id int
);
--创建从表，有两个外键
create table purchase( 
	order_id int primary key,
    customer_id int,
    goods_id int,
    nums int,
    foreign key (customer_id) references customer(customer_id),
    foreign key (goods_id) references goods(goods_id)
);
```

# 八、自增长

问题引出：

在某张表中，存在一个id列（整数），我们希望在添加记录的时候，该列从1开始，自动的增长，怎么处理？

```sql
--基础语法:结合主键或者unique使用
字段名 整型 [primary key]|unique auto_increment
--添加 自增长的字段方式（3种）
insert into xxx(字段1，字段2...) values(null,'值'...);
insert into xxx(字段2...) values('值1','值2'...);
insert into xxx values(null,'值1'...);

--案例演示：
--创建一张表
create table t2(
	id int primary key auto_increment,
    email varchar(20)not null default '',
    name varchar(20)not null default ''
);
desc t2; --查看表结构，就可以发现id字段是自增长的
--自增长使用
--方法1：
insert into t2
	values(null,'jack@qq.com',"jack"); 
	--null是给id字段的，因为是空值，所以使用自增长来维护，填入1
insert into t2
	values(null,'tom@qq.com',"tom"); --tom的id为自增长的2
--方法2：
insert into t2
	(email,name) values("lsx@qq.com",'lsx');
--方法3：

--自定义 自增长起始值
create table t3(
	id int primary key auto_increment,
    email varchar(20)not null default '',
    name varchar(20)not null default ''
);
alter table t3 auto_increment = 10;
insert into t3
	values(null,'jack@qq.com',"jack"); 
```

细节补充：

1. 一般来说，自增长是和primary key配合使用的；

2. 自增长也可以单独使用，但需要配合一个unique；

3. 自增长修饰的字段为整数型的，虽然小数也可以，但是并不常用；

4. 自增长默认从1开始，也可以通过如下指令修改：

   `alter table tab_name auto_increment = new_start;`

5. 如果你添加数据时，给自增长字段（列）指定的有值，则以指定的值为准，后面的值在这个值基础上自增长。一般很少这么做。

6. 一般来说，如果定义了自增长，就按照自增长的规则来添加数据，而不指定数据，否则就没必要定义自增长了

# 九、mysql索引

## 9.1案例演示

说起提高数据库性能，索引是最物美价廉的东西了，不用加内存，不用改程序，不用调sql，查询速度就可能提高百倍千倍。

索引是帮助mysql高效获取数据的排好序的数据结构。

mysql索引使用的是B+树索引。Redis使用的是红黑树索引。

![image-20210608112942955](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210608112942955.png)

红黑树也是二叉树，mysql没有使用红黑树索引，因为数据量大的时候，最差情况仍然要查找到叶子节点。

这里我们举例说明索引的好处【构建海量表8000000】

```sql
--终端执行sql文件的方法：source path（sql文件路径）
--这里使用NAVIcat来执行sql文件，8000000行记录还是很多的，大概要创建5分钟
--在没有创建索引时，我们查询一条记录：
select * from emp where empno = 1234567
--大概花了4秒，这还是单机查询，对计算机来说，这简直无法忍受

--使用索引来优化一下
--目前来说，emp所有数据是放在data目录中的emp.ibd中的
--没有创建索引前，emp.ibd文件大小为524M
--创建索引
--on emp (empno) : 表示在emp表的empno列创建索引
create index empno_index on emp(empno)
--执行了10秒左右，刚刚那个文件变大了，变成了655M左右
--创建的索引本身也是占内存空间的，牺牲了空间来提高查询效率
--创建索引后，重新查询
select * from emp where empno = 123456
--为了避免缓存，这里换用一个编号，发现查询速度仅用了0.017秒
--创建索引后，只对创建了索引的列有效
```

ename上没有建立索引会怎么样？

select * from emp where ename = 'axJxC';

ename上没有建立索引，按照这个字段来查找，速度依然是很慢的，自行演示。

是不是建立一个索引就能解决所有的问题？

由上述案例可以看出，创建一个索引并不能解决所有的问题，因为一个索引只对一个字段生效。

## 9.2索引原理

当没有索引时，会进行全表扫描：

![image-20210511164332403](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210511164332403.png)

即使刚开始就匹配到了1，后面也会接着扫描，因为mysql并不知道后面还有没有匹配的记录。所以查询速度是很慢的。

建立索引之后，例如在id字段建立索引，就会形成一棵二叉树：

![image-20210511164746334](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210511164746334.png)

二叉树是利于检索的。例如寻找1，就直接在5的左子树中去找，直接效率翻倍。

思考：如果比较了30次，那么覆盖的范围就是2^30次方，很大的范围，所以建立索引是效率很高的。

另外值得注意的是，如果对表进行dml(修改，删除，添加)会对索引进行维护，对速度有影响。

总结索引的代价：

1. 磁盘占用；
2. 对dml(update delete insert)语句的效率影响。

但并不是说索引就没有存在意义了，因为在实际的项目中，select远比dml加起来多的多，百分之90都是在select。所以索引带来更多的是好处。

## 9.3索引分类

- 主键索引，主键自动的为主索引（类型为 Primary key）

- 唯一索引 （UNIQUE)

- 普通索引（INDEX）

  主键索引和唯一索引不允许两个记录有相同的字段值，但有些时候是需要允许他们有相同值的，比如两个人姓名可以相同，那么就可以创建为普通索引。

- 全文索引（FULLTEXT）[适用于MyISAM]

  全文索引是针对某篇文章建的索引，例如想要查找某篇文章中有没有“电影”这个词，那么就可以针对这篇文章建立一个索引。

  mysql自带全文索引，但不好用，所以开发中考虑使用：全文搜索Solr 和 ElasticSearch（ES）。

## 9.4索引使用

### 9.4.1创建索引

基本语法

```sql
create table t4(
	id int ,
    name varchar(20)
);
--查询表是否有索引
show indexes from t4;
--添加索引
--方法1：
create [unique] index index_name on tab_name
	(col_name[(length)][asc|desc],...);
--方法2：
alter table table_name add index [index_name](index_col_name,...)

--添加主键索引（索引）
alter table tab_name add primary key(列名,...);

--删除索引
drop index index_name on tab_name;

--删除主键索引 比较特别：
alter table tab_name drop primary key

```

案例

```sql
--添加唯一索引
--某一列只能添加一种索引类型，对某一列重复添加会报错
create unique index id_index on t4 (id);
--查询索引之后，Non_unique代表这个索引是否是唯一索引，0是唯一索引，1不是唯一索引
--添加普通索引方式1：
create index id_index on t4 (id);
--添加普通索引方:2：
alter table t4 add index id_index (id);
--如何选择唯一索引或普通索引？
--如果某列的值是不重复的，则优先考虑使用unique索引，因为这个比较快；否则使用普通索引

--添加主键索引
--方法1：创建表的时候直接指定主键，这里就不演示了
--方法2：
alter table t4 add primary key(id);
```

### 9.4.2删除索引

```sql
--删除索引
drop index id_index on tab_name;

--删除主键索引;
alter table tab_name drop primary key
```

### 9.4.3修改索引

修改索引只需要先删除掉索引，再添加新的就可以。

### 9.4.4查询索引

```sql
--方式1：
show index from tab_name;

--方式2：
show indexes from tab_name;

--方式3：
show keys from tab_name;

--方式4：
desc tab_name;
--推荐使用前三种，第四种展示的信息不完善
```

### 9.5.5索引练习

![image-20210512134347988](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210512134347988.png)

![image-20210512134446944](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210512134446944.png)

## 9.5小结

在哪些列上适合使用索引呢？

1. 较频繁的作为查询条件字段，应创建索引
2. 唯一性太差的字段不适合单独创建索引，即使频繁作为查询条件
3. 更新非常频繁的字段不适合创建索引
4. 不会出现在where子句中的字段不该创建索引

#  十、事务

## 10.1基础概念

- 什么是事务？

  事务是用于保证数据的一致性，它由一组相关的dml语句（增删改，不包括select）组成，该组的dml语句要么全部成功，要么全部失败。如：转账就要用事务来处理，用以保证数据的一致性。

- 事务的理解（示意图）

  ![image-20210512140139707](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210512140139707.png)

- 事务和锁

  当执行事务操作时（dml语句），mysql会在表上加锁，防止其他用户改表的数据。这对用户来讲是非常重要的。

- 回退事务

  在介绍回退事务前，先介绍一下保存点（save point).保存点是事务中的点，用于取消部分事务。当事务结束时（commit），会自动的删除该事务所定义的所有保存点。

  当执行回退事务时，通过指定保存点，可以回退到指定的点。

- 提交事务

  使用commit语句可以提交事务，当执行了commit语句之后，会确认事务的变化、结束事务、删除保存点、释放锁，数据生效。当使用commit语句结束事务之后，其他会话(其他连接)将可以查看到事务变化后的新数据。

  补充：在事务执行过程中，如果有其他用户也登录了mysql，他不一定能看到你执行过程中的全部数据的，这里牵扯到一个隔离级别的问题，决定了他可以看到多少数据。

- mysql数据库控制台事务的几个重要操作

  1. start transaction --开始一个事务
  2. savepoint point_name--设置保存点
  3. rollback to  point_name--回退事务
  4. rollback --回退全部事务
  5. commit --提交事务，所有的操作生效，不能回退

  保存点类似于虚拟机快照。

```sql
--事务演示
--1.创建一张测试表
 create table t2(
    id int,
    name varchar(20));
--2.开始事务，此时已经默认设置了一个保存点，可以rollback直接回退的   
start transaction;
--3.设置保存点1
savepoint a;
--执行dml操作
insert into t2 values(1,"孙悟空");
--4.设置保存点2
savepoint b;
--执行dml操作
insert into t2 values(2,"贝吉塔");
--5.回退事务b
rollback to b;
--6.回退事务a
rollback to a;
--7.如果直接rollback，那么直接回到第一个保存点
rollback;
--注意：如果直接回退到较早的保存点，那么后续的保存点会被删除掉，不能再回去
--8.提交事务之后，就不能再回退了，保存点都会删除
commit;
```

细节补充：

1. 如果不开始事务，默认情况下，dml操作是自动提交的，不能回滚
2. 如果开始一个事务，没有创建保存点，可以执行rollback，默认就是回退到事务开始的状态
3. 也可以在这个事务中（还没有提交时），创建多个保存点，上述演示过程有示范
4. 可以在事务没有提交前，选择回退到哪个保存点
5. mysql的事务机制需要innodb的存储引擎才可以使用，myisam不好使用
6. 开始一个事务：start transaction，或者 set autocommit=off；两种方式都可以。

## 10.2事务的隔离级别

### 10.2.1概念

mysql隔离级别定义了事务与事务之间的隔离程度。

隔离级别从上到下逐渐增强。

![image-20210512154855635](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210512154855635.png)

1. 多个连接开启各自事务操作数据库中的数据时，数据库系统要负责隔离操作，以保证各个连接在获取数据时的准确性。

2. 如果不考虑隔离性，可能会引发如下问题：

   - 脏读：当一个事务读取另一个事务尚未提交的修改时，产生脏读，因为别人有可能放弃提交或者回滚，那读到的数据就没有意义了；
   - 不可重复读：同一查询在同一事务中多次进行，由于其他提交事务所做的修改或删除，每次返回不同的结果集，此时发生不可重复读；
   - 幻读：同一查询在同一事务中多次进行，由于其他提交事务所做的插入操作，每次返回不同的结果集，此时发生幻读。幻读强调的是查询到了之前查询没有看到过的数据。

   可以参考这篇文章来理解：

   [大白话讲解脏写、脏读、不可重复读和幻读 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/150107974)

3. 查看当前会话的隔离级别：

   `select @@tx_isolation;`

4. 查看当前系统隔离级别，所有用户在登陆系统时候的隔离级别：

   `select @@global.tx_isolation;`

5. 设置当前会话隔离级别：

   `set session transaction isolation level read uncommitted;`

6. 设置当前系统隔离级别，所有用户在登陆系统时候的隔离级别：

   set global transaction isolation level read uncommitted;

7. mysql默认的事务隔离级别是 repeatable read,一般情况下，没有特殊要求，没有必要修改（因为该级别可以满足绝大部分项目需求）

### 10.2.2案例

我们举一个案例来说明mysql的事务隔离级别，以对account表进行操作为例。（id，name，money）。

为了看到隔离级别的效果，我们需要分别使用两个控制台登陆到mysql，模拟多用户。

![image-20210512155217342](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210512155217342.png)

#### 读未提交

```sql
--演示
--1.登陆之后，查看当前mysql的隔离级别
select @@tx_isolation; --默认是可重复读
--2.将控制台1隔离级别设置成读未提交
set session transaction isolation level read uncommitted;
--隔离级别是和事务相关的，所以一定要先启动事务，才能讨论隔离级别
--3.两个控制台都启动事务
start transaction;
--4.创建表，这里是在控制台2操作的，创建完成之后，两个控制台应该都可以看到一个空表
create table account(
	id int,
	name  varchar(32),
	money int);
--5.脏读测试
--在控制台2中执行插入操作，为account表插入一条记录
insert into account
	values(1,'goku',6000);
--此时控制台2并未执行提交操作，但控制台1可以查询到这条记录，产生了脏读
--读未提交的隔离级别很低，最容易引发问题
--6.不可重复读&幻读测试，两者主要区别在于操作不同，不可重复度是删除和修改；幻读是针对插入操作
--6.1修改之前的表记录，在控制台2中操作：
update account set money = 8000 
	where id = 1;
--6.2新插入一条记录
insert into account
	values(2,'贝吉塔',5500);
--此时控制台2并未提交，但是控制台1可以看到两个操作的结果，产生不可重复读以及幻读
--控制台2对控制台1产生了影响，这是不对的。
--正常的应该是，我连到了mysql，那么我读取到的数据就应该是我连接时候数据库中的数据，不受其他因素影响
--记得提交两个事务，否则无法开启新的事务
```

#### 读已提交

```sql
--1.启动事务并设置控制台1隔离级别
start transaction;
set session transaction isolation level read committed;
--2.在控制台2执行插入操作
insert into account
	values(1,'比克',4000);
--此时若不提交，控制台1是看不到插入记录的，不会产生脏读
--但是不可重复读和幻读还会产生，自行实验。
```

#### 可重复读

```sql
--自行实验
```

#### 可串行化

加锁的意思是，如果他发现某一张表正在被操作没有提交，就会卡在这里，不会操作。需要退出重连控制台。

```sql
--自行实验
```

## 10.3mysql事务ACID

事务的acid特性

1. 原子性（Atomicity）

   原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。

2. 一致性（Consistency）

   事务必须使数据库从一个一致性状态变换到另一个一致性状态。

3. 隔离性（Isolation）

   事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。

4. 持久性（Durability）

   持久性是指一个事务一旦被提交，他对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响。

## 10.4事务的课堂练习

1. 登陆mysql控制客户端A，创建表Dog(id,name)，开始一个事务，添加两条记录；
2. 登陆mysql控制客户端B，开始一个事务，设置为读未提交；
3. A客户端修改Dog一条记录，不要提交，看看B客户端是否看到变化，说明什么问题？
4. 登陆mysql客户端C，开始一个事务，设置为读已提交，这时A客户修改一条记录，不要提交，看看C客户端是否看到变化，说明什么问题？

# 十一、存储引擎

存储引擎是表级别，而非数据库级别的。当然数据库级别也可以设置，但最终生效是表级别的。

## 11.1表类型和存储引擎

基本概念：

1. mysql的表类型由存储引擎（Storage Engines）决定，主要包括MyISAM、innoDB、Memory等；不同表类型使用起来也有很大差异；
2. mysql数据表主要支持6种类型，分别是：CSV、Memory、ARCHIVE、MRG_MYISAM、MYISAM、InnoBDB；实际开发中要根据需要灵活选取自己的表类型；
3. 这6种又分为两类，一类是“事务安全型”（transaction-safe），比如：InnoDB；这类是支持事务的；其余都属于第二类，称为“非事务安全型”（non-transaction-safe）[mysiam 和 memory]这种是不支持事务的，表会更快一些。

```sql
--查看所有的存储引擎
show engines;
--修改表的存储引擎
alter table tab_name engine = 存储引擎;
```

主要的表的存储引擎/表类型的特点（重要）：

![image-20210512174435211](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210512174435211.png)

细节说明：

重点介绍三种：MyISAM、InnoDB、MEMORY.

1. MyISAM不支持事务，也不支持外键，但其访问速度快，对事务完整性没有要求；

2. InnoDB存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全，但是比起MyISAM存储引擎，InnoDB写的处理效率差一些，并且会占用更多的磁盘空间以保留数据和索引。

3. MEMORY存储引擎使用存在内存中的内容来创建表。每个MEMORY表只实际对应一个磁盘文件，MEMORY类型的表访问速度非常快，因为他的数据是放在内存中的，并且默认使用HASH索引，但是一旦mysql服务关闭，表中的数据就会丢失掉，表的结构还在。

   不需要持久化的数据（很常见），但是经常查询的数据，可以考虑使用MEMORY存储引擎。

## 11.2案例演示

### 11.2.1innodb存储引擎

这个存储引擎我们之前一直使用，这里就不在多赘述。主要有以下几个特点：

1. 支持事务
2. 支持外键
3. 支持行级锁

innoDB索引实现（聚集索引）

- 表数据文件本身就是按B+Tree组织的一个索引结构文件

- 聚集索引-叶节点包含了完整的数据记录

- 为什么innoDB表必须建主键，并且推荐使用整型的自增主键？

  需要使用这个主键来构建B+树结构。否则mysql会自动找一列不重复的记录（若不存在，则自行维护一列数据）来完成。为了节约数据库资源，推荐自己建主键。

  推荐整型（而不是常见的uuid）作为主键，主要是可以节省存储空间，并且比较效率比字符串高。

- 为什么非主键索引结构叶子节点存储的是主键值？（一致性和节省存储空间）

### 11.2.2myisam引擎

特点：

1. 添加速度快
2. 不支持事务和外键
3. 支持表级锁

```sql
--创建一张表
create table t3(
	id int,
	name varchar(20))
    engine myisam;
--测试他是不是不支持事务
start transaction;
savepoint a;
insert into t3 values(1,"goku");
select * from t3;
rollback to a; --尝试回滚
select * from t3; --查询发现数据依然存在，没有会滚成功
```

### 11.2.3memory引擎

特点：

1. 数据存储在内存中
2. 执行速度很快（没有IO读写）
3. 默认支持索引（hash表）

```sql
--创建一张表，指定引擎为memory
create table t4(
	id int,
	name varchar(20))
    engine memory;
--速度的话，可以插入很多数据去查询验证，这里不演示
--插入数据
insert into t4
	values(1,"goku"),(2,"贝吉塔");
select * from t4;
--此时如果重启（关闭）mysql服务，那么数据就丢失了，表结构仍然存在
```

## 11.3如何选择

我们该如何选择表的存储引擎呢？

1. 如果你的应用不需要事务，处理的只是基本的CRUD操作，那么myisam是不二选择，速度快；

2. 如果需要支持事务，选择InnoDB；

3. memory存储引擎就是将数据存储在内存中，由于没有磁盘I/O的等待，速度极快，但由于是内存存储引擎，所做的任何修改在服务器重启后都将消失。

   经典的用法是用户的在线状态保存。在线状态的保存是没必要放在磁盘中的，因为他可能变化的很频繁。

# 十二、mysql视图

问题引出：

我们来看一个需求，emp表的列信息有很多，有些信息是个人重要信息，比如sal，comm，mgr，hiredate，如果我们希望某个用户只能查询emp表的empno、ename、job和deptno信息，其他信息隐藏起来，有什么办法呢？

其中一个方案就是使用视图，我们把某些列摘出来，视图权限给到某个用户，其他列隐藏起来。

## 12.1基本概念

1. 视图是一个虚拟表，其内容由查询定义，同真实的表一样，视图包含列，其数据来自对应的真实表（基表）
2. 视图可以基于多张基表来创建；
3. 基表的改变，会影响到视图的数据，通过视图也可以修改基表的数据。
4. 视图和基表关系的示意图： 

![image-20210513111049233](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210513111049233.png)

## 12.2基本用法

### 12.2.1语法

视图的基本使用方法：

1. create view 视图名 as select 语句
2. alter view 视图名 as select语句 --更新成新的视图
3. show create view 视图名
4. drop view 视图名1，视图名2

```sql
--案例：
--创建一个视图emp_view01,只能查询emp表的（empno、ename、job和deptno）信息
--创建视图
create view emp_view01
	as
	select empno,ename,job,deptno from emp;
	
--修改视图
update emp_view01
	set job = "MANAGER"
	where empno = 7369 --修改视图是会影响到基表的

--查看视图
desc emp_view01;

select * from emp_view01;
select empno,job,sal from emp_view01;

--查看创建视图的指令
show create view emp_view01;

--视图中可以再使用视图
--比如从emp_view01视图中，选出empno，和ename做出新视图
create view emp_view02
	as 
	select empno,ename from emp_view01;

--删除视图
drop view emp_view01;

```

### 12.2.2细节补充

1. 创建视图之后，到数据库中去查看，对应视图只有一个视图结构文件（形式：视图名.frm）
2. 视图的数据变化会影响到基表，基表的数据变化也会影响到视图（insert、update、delete）
3. 视图中可以再使用视图。数据仍然来自基表。

## 12.3实践

视图的意义主要在于以下几个方面：

1. 安全：一些数据表有着重要的信息，有些字段是保密的，不能让用户直接看到。这个时候就可以创建一个视图，在这张视图中只保留一部分字段，这样，用户就可以查询自己需要的字段，不能查看保密的字段；
2. 性能：关系数据库的数据常常会分表存储，使用外键建立这些表的之间关系。这时，数据库查询通常会用到连接join。但这样做不但很麻烦，效率相对也比较低。如果建立一个视图，将相关的表和字段组合在一起，就可以避免使用join查询数据。
3. 灵活：如果系统中有一张旧的表，这张表由于设计的问题，即将被废弃。然而，很多应用都是基于这张表，不易修改。这时就可以建立一张视图，视图中的数据直接映射到新建的表。这样，就可以少做很多改动，也达到了升级数据表的目的。

```sql
--练习
--针对emp，dept和salgrade三张表，创建一个视图emp_view03，可以显示雇员编号，雇员名，雇员部门名称和薪水级别（即使用三张表，构建一个视图）
--分析：使用三表联合查询，限制条件至少要有两个，才不会出现笛卡尔集
select *
	from emp,dept,salgrade
	where emp.deptno = dept.deptno and
	(sal between losal and hisal);
--创建视图
create view emp_view03
	as
	select empno,ename,dname,grade
	from emp,dept,salgrade
	where emp.deptno = dept.deptno and
	(sal between losal and hisal);

desc emp_view03;
select * from emp_view03;
```

实际工作中，可以考虑使用视图来优化自己的项目。

# 十三、mysql管理

## 13.1mysql用户



mysql中的用户，都存储在系统数据库mysql中user表中。

为什么要有用户管理呢？

当我们做项目开发时，可以根据不同的开发人员，赋给他相应的mysql操作权限。所以mysql数据库管理人员（root），根据需要创建不同的用户，赋给相应的权限，供开发人员使用。

其中user表的重要字段说明：

1. host：允许登陆的"位置"，localhost表示该用户只允许本机登陆，也可以指定ip地址，比如：192.168.1.100
2. user：用户名；
3. authentication_string：密码，是通过mysql的password()函数加密之后的密码。

## 13.2基本操作

- 创建用户：

  create user "用户名" @ "允许登陆位置" identified by "密码"

  说明：创建用户，同时指定密码

- 删除用户：

  drop user "用户名" @ "允许登陆位置"；

- 修改密码：

  1. 修改自己的密码：

     set password ＝ password("密码");

  2. 修改他人密码（需要有修改用户密码的权限）：

     set password for "用户名"@"登陆位置" = password('密码');

```sql
--1.创建新的用户
--注意，一旦创建成功之后，信息是以加密的形式存在的，而不是明文信息
--@前后不要有空格
create user "lsx_fir"@"localhost" identified by "1234567";

--查看用户信息
select * from mysql.user;

--2.删除用户
drop user "lsx_fir"@"localhost"

--登陆演示过很多次，这里不做展示

--修改密码
--以修改自己的密码为例：
set password = password("123");
```

不同的数据库用户，登录到DBMS之后，根据相应的权限，可以操作的库和数据对象（表、视图、触发器）都不一样。

![image-20210513141057969](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210513141057969.png)

## 13.3用户权限

### 13.3.1权限种类

- `All/All Privileges权限代表全局或者全数据库对象级别的所有权限`
- <u>Alter权限代表允许修改表结构的权限，但必须要求有create和insert权限配合。如果是rename表名，则要求有alter和drop原表， create和insert新表的权限</u>
- Alter routine权限代表允许修改或者删除存储过程、函数的权限
- <u>Create权限代表允许创建新的数据库和表的权限</u>
- Create routine权限代表允许创建存储过程、函数的权限
- Create tablespace权限代表允许创建、修改、删除表空间和日志组的权限
- Create temporary tables权限代表允许创建临时表的权限
- <u>Create user权限代表允许创建、修改、删除、重命名user的权限</u>
- <u>Create view权限代表允许创建视图的权限</u>
- <u>Delete权限代表允许删除行数据的权限</u>
- <u>Drop权限代表允许删除数据库、表、视图的权限，包括truncate table命令</u>
- Event权限代表允许查询，创建，修改，删除MySQL事件
- Execute权限代表允许执行存储过程和函数的权限
- File权限代表允许在MySQL可以访问的目录进行读写磁盘文件操作，可使用的命令包括load data infile,select … into outfile,load file()函数
- Grant option权限代表是否允许此用户授权或者收回给其他用户你给予的权限,重新付给管理员的时候需要加上这个权限
- Index权限代表是否允许创建和删除索引
- <u>Insert权限代表是否允许在表里插入数据，同时在执行analyze table,optimize table,repair table语句的时候也需要insert权限</u>
- Lock权限代表允许对拥有select权限的表进行锁定，以防止其他链接对此表的读或写
- Process权限代表允许查看MySQL中的进程信息，比如执行show processlist, mysqladmin processlist, show engine等命令
- Reference权限是在5.7.6版本之后引入，代表是否允许创建外键
- Reload权限代表允许执行flush命令，指明重新加载权限表到系统内存中，refresh命令代表关闭和重新开启日志文件并刷新所有的表
- Replication client权限代表允许执行show master status,show slave status,show binary logs命令
- Replication slave权限代表允许slave主机通过此用户连接master以便建立主从复制关系
- <u>Select权限代表允许从表中查看数据，某些不查询表数据的select执行则不需要此权限，如Select 1+1， Select PI()+2；而且select权限在执行update/delete语句中含有where条件的情况下也是需要的</u>
- <u>Show databases权限代表通过执行show databases命令查看所有的数据库名</u>
- <u>Show view权限代表通过执行show create view命令查看视图创建的语句</u>
- Shutdown权限代表允许关闭数据库实例，执行语句包括mysqladmin shutdown
- Super权限代表允许执行一系列数据库管理命令，包括kill强制关闭某个连接命令， change master to创建复制关系命令，以及create/alter/drop server等命令
- Trigger权限代表允许创建，删除，执行，显示触发器的权限
- <u>Update权限代表允许修改表中的数据的权限</u>
- <u>Usage权限是创建一个用户之后的默认权限，其本身代表连接登录权限</u>

### 13.3.2用户赋权/回收权限

基本语法

赋予权限：

grant 权限列表 on 库.对象名 to '用户名'@'登陆位置' [identified by '密码']

说明：

1. 权限列表，多个权限用逗号分开

   grant select on ...

   grant select , delete ,create on ...

   grant all [privileges] on...  //表示赋予该用户在该对象上的所有权限

2. 特别说明

   *.* :代表本系统中所有数据库的所有对象（表，视图，存储过程）

   库.* :表示某个数据库中的所有数据对象（表，视图，存储过程等）

3. identified by 可以省略，也可以写出

   - 如果用户存在，就是修改该用户的密码
   - 如果该用户不存在，就是创建该用户

如果权限没有生效，可以执行下面的命令：`flush privileges;`

通常来讲，mysql5.7以后的版本，赋权之后就立马生效了。

回收权限：

revoke 权限列表 on 库.对象名 from '用户名'@'登陆位置';

### 13.3.3练习

题目：

1. 创建一个用户（你的名字，拼音），密码123，并且只可以从本地登陆，不让远程登陆
2. 创建库和表testdb下的news表，要求：使用root用户创建
3. 给用户分配查看news表和添加数据的权限
4. 测试看看用户是否只有这几个权限
5. 修改密码为abc，要求：使用root用户完成
6. 演示回收权限
7. 重新登陆
8. 使用root用户删除你的用户

```sql
--演示用户权限的管理
--创建用户 lsx02 密码 123，从本地登陆
create user 'lsx02'@'localhost' identified by '123';

--使用root用户创建testdb,表news
create databases testdb;
create table news(
	id int,
    content varchar(32)
);
--添加一条测试数据
insert into news values(100,"北京新闻");
select * from news;

--给lsx02分配查看news表和添加news的权限
grant select , insert 
	on testdb.news
	to 'lsx02'@'localhost'
--这个时候lsx02就有了查询和插入news表的权限
--可以不断增加权限的
grant update 
	on testdb.news
	to 'lsx02'@'localhost'
	
--回收lsx02用户在testdb.news表的所有权限
revoke select,update,insert on testdb.news from 'lsx02'@'localhost'
revoke all on testdb.news from 'lsx02'@'localhost'

--删除lsx02用户
drop user 'lsx02'@'localhost'
```

13.3.4细节补充

1. 在创建用户的时候，如果不指定Host，则为%，%表示所有IP都有连接权限

   （可能性比较小，一般是不会允许从任意ip登陆的）

   create user xxx;

2. 你也可以这样指定：

   create user 'xxx'@'192.168.1.%'表示xxx用户在192.168.1.*的ip可以登陆mysql

3. 在删除用户的时候，如果host不是%，需要明确指定'用户'@'host值'

```sql
--演示
create user jack --所有ip都可以连接
select `host`,`user` from mysql.user

create user 'smith'@'192.168.1.%'

drop user jack --默认就是drop user 'jack'@'%'
--如果host不是%
drop user 'smith'@'192.168.1.%' --必须这样删除，否则会失败

```

