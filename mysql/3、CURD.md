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
