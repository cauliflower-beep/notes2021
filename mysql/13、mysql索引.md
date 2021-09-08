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

