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
