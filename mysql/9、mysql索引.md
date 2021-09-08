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
