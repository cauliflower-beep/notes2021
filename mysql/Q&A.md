# Q&A

## 1.索引的基本原理

​	索引用来快速的寻找那些具有特定值的记录。如果没有索引，一般来说执行查询时会遍历整张表。

​	索引的原理：把无序的数据变成有序的查询。

1. 把创建了索引的列的内容进行排序；
2. 对排序结果生成倒排表；
3. 在倒排表内容上拼上数据地址链；
4. 在查询的时候，先拿到倒排表内容，再取出数据地址链，从而拿到具体数据。

## 2.聚簇索引和非聚簇索引的区别

​	首先，他们都是B+树的数据结构。

- 聚簇索引：将数据存储与索引放到了一起，并且是按照一定的顺序组织的，找到索引也就找到了数据，数据的物理存放顺序与索引顺序是一致的，即：只要索引是相邻的，那么对应的数据一定也是相邻的存放在磁盘上的。
- 非聚簇索引：叶子节点不存储数据，存储的是数据行地址，也就是说要根据索引查找到数据行的位置再取磁盘查找数据，这个就有点类似于一本书的目录。比如我们要找到第三章第一节，那我们先在这个目录里面找，找到对应的页码后再去对应的页码看文章。

​	优势（聚簇索引）：

1. 查询通过聚簇索引可以直接获取数据，相比非聚簇索引需要第二次查询（非覆盖索引的情况下）效率要更高；

   ps.覆盖索引是指，查数据的时候只需要索引，不需要数据，比如 select id from *，这个id就是索引，不需要其他诸如name等字段，就属于覆盖索引；

2. 聚簇索引对于范围查询的效率很高，因为其数据是按照大小排列的；

3. 聚簇索引适合用在排序的场合，非聚簇索引不适合。

​	劣势（聚簇索引）：

1. 维护索引的代价很昂贵，特别是插入新行或者主键被更新，索引要保持顺序，就要分页（page split）的时候，可能会有大量的记录要被移动到后面的页，效率比较低。建议在大量插入新行后，选在负载较低的时间段。通过optimize table优化表，因为必须被移动的行数据可能造成碎片。使用独享表空间可以弱化碎片；

2. 表因为使用uuid（随机id）作为主键，使数据存储稀疏，这就会出现聚簇索引有可能比全表扫描更慢，所以建议使用int的auto_increment作为主键；

3. 如果主键比较大的话，那辅助索引将会变得很大。因为辅助索引的叶子存储的是主键值；过长的主键值，会导致非叶子节点占用更多的物理空间。

   ps.辅助索引是在聚簇索引之上建立的一层索引，可以理解为是一种非聚簇索引，只不过它存储的不是数据地址，而是聚簇索引的主键id，查找数据的时候，是先从辅助索引中取到id，然后再去聚簇索引中取数据。

​	InnoDB中一定有主键，不设置也会有。主键一定是聚簇索引，不手动设置，则会使用unique（唯一）索引，没有unique索引，则会使用数据库内部的一个行的隐藏id来当作主键索引。在聚簇索引之上创建的索引称之为辅助索引，辅助索引访问数据总是需要二次查找，非聚簇索引都是辅助索引，像复合索引、前缀索引、唯一索引，辅助索引叶子节点存储的不再是行的物理位置，而是主键值。

​	MyISM使用的是非聚簇索引，没有聚簇索引，非聚簇索引的两棵B+树看上去没什么不同，节点的结构完全一致，知识存储的内容不同而已，主键索引B+树的节点存储了主键，辅助键索引B+树存储了辅助键。表数据存储在独立的地方，这两棵B+树的叶子节点都使用一个地址指向真正的表数据，对于表数据来说，这两个键没有任何差别。由于索引树是独立的，通过辅助键检索无需访问主键的索引树。

​	如果涉及到大数据量的排序、全表扫描、count之类的操作的话，还是MyISM占优势些，因为索引所占空间小，这些操作是需要在内存中完成的。

## 3.mysql索引的数据结构，各自优劣

​	索引的数据结构和具体存储引擎的实现有关，在mysql中使用较多的索引有hash索引，B+树索引等，InnoDB存储引擎的默认索引实现为：B+树索引。对于哈希索引来说，底层的数据结构就是哈希表，因此在巨大多数需求为单条记录查询的时候，可以选择哈希索引，查询性能最快；其余大部分场景，建议选择BTree索引。

​	B+树：

​	B+树是一个平衡的多叉树，从根节点到每个叶子节点的高度差值不超过1，而且同层级的节点间有指针互相链接。在B+树上的常规检索，从根节点到叶子节点的搜索效率基本相当，不会出现大幅波动，而且基于索引的顺序扫描时，也可以利用双向指针快速左右移动，效率非常高。因此，B+树索引被广泛应用于数据库、文件系统等场景。

​	哈希索引：

​	哈希索引就是采用一定的哈希算法，把键值换算成新的哈希值，检索时不需要类似B+树那样从根节点到叶子节点

