参考资料：

- 官方explain文档：

<https://dev.mysql.com/doc/refman/5.7/en/explain-output.html>

- mysql explain详解

<https://cloud.tencent.com/developer/article/1093229>





# 索引简介

- 为了加快查询而创建的数据结构，通过对数据进行排序而加快查询

- 底层实现一般是B+树

- 优势：
  - 提高检索效率，降低IO成本
  - 通过索引列对数据进行排序，降低数据排序成本，降低了CPU的消耗

- 劣势：
  - 索引需要占用空间
  - 更新表的数据，同时需要更新索引



# MySQL索引分类

1. 单值索引

   一个索引只包含单个列

2. 唯一索引

   索引列的值必须唯一，但允许有空值

3. 复合索引

   一个索引包含多个列



# 基本语法

## 1.创建

- CREATE [UNIQUE] INDEX indexName ON mytable(columnName(length))

- ALTER TABLE mytable ADD [UNIQUE] INDEX [indexName] ON (columnName(length))

## 2.删除

- DROP INDEX [indexName] ON mytable

## 3.查看

- SHOW INDEX FROM tablename
- SHOW KEYS from tablename

## 4.使用ALTER命令

- ALTER TABLE tablename ADD PRIMARY KEY(column_list)

  添加一个主键，索引值必须唯一，不能为NULL

- ALTER TABLE tablename ADD UNIQUE index_name(column_list)

  索引值唯一，可以出现NULL，NULL可以出现多次

- ALTER TABLE tablename ADD INDEX index_name(column_list)

  普通索引，索引值可以出现多次

- ALTER TABLE tablename ADD FULLTEXT index_name(column_list)

  全文索引

  

# MySQL索引结构

1.BTree索引



2.Hash索引



3.full-text全文索引



4.R-Tree索引



# 创建索引原则

## 什么情况下建索引

1. 主键自动建立唯一索引

2. 频繁作为查询条件的字段应该创建索引

3. 查询中与其它表关联的字段，外键关系建立索引

4. 在高并发下倾向创建组合索引
5. 查询中的排序字段
6. 查询中统计或分组字段



## 什么情况不建索引

1. 表记录太少

2. 经常增删改的表

3. 某个字段包含很多重复的值，比如性别字段，取值男女，建立索引没有意义

4. 频繁更新的字段不适合创建索引

   因为每次更新不仅更新数据，还会更新索引

5. where条件里用不到的字段不创建索引



# explain关键字

## 1.介绍

查看sql语句的执行计划

使用explain关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句的。分析你的查询语句或是表结构的性能瓶颈

## 2.使用

explain sql语句

## 3.执行结果解释

### id

id越大越先执行



### select_type

​      查询中每个select子句的类型

(1) SIMPLE(简单SELECT,不使用UNION或子查询等)

(2) PRIMARY(查询中若包含任何复杂的子部分,最外层的select被标记为PRIMARY)

(3) UNION(UNION中的第二个或后面的SELECT语句)

(4) DEPENDENT UNION(UNION中的第二个或后面的SELECT语句，取决于外面的查询)

(5) UNION RESULT(UNION的结果)

(6) SUBQUERY(子查询中的第一个SELECT)

(7) DEPENDENT SUBQUERY(子查询中的第一个SELECT，取决于外面的查询)

(8) DERIVED(派生表的SELECT, FROM子句的子查询)

(9) UNCACHEABLE SUBQUERY(一个子查询的结果不能被缓存，必须重新评估外链接的第一行)



### table

(1) 表的名字

(2) <derivedx>   x是个数字,是id = x执行的结果 



### type  

这一列说明MySQL决定如何查找表中的行

取值有： ALL, index,  range, ref, eq_ref, const, system, NULL（从左到右，性能从差到好）

- **ALL** 全表扫描

- **index** 

  全索引扫描，索引文件比数据文件小，比全表扫描好，减少了磁盘IO

  一般用在select的数据已经在索引中

- **range** 根据索引扫描一定范围的数据，如between,in,>,<

- **ref**  

  索引要和某个值相比较，可能会找到多个符合条件的行。

- **eq_ref** 

  通过索引只能确定一行数据  比如primary key, unique not null

- **const ，system**

  mysql能对查询的某部分进行优化并将其转化成一个常量（可以看show warnings 的结果）。用于 primary key 或 unique key 的所有列与常数比较时，所以表最多有一个匹配行，读取1次，速度比较快。

*注意：一般重点优化ALL*



### possible_keys, key, key_len

- possible_keys 理论上会被用到的索引

- key 实际上被用到的索引

- key_len 

  查询时使用的索引的长度（字节）

  表示查询优化器使用了索引的字节数. 这个字段可以评估组合索引是否完全被使用, 或只有最左部分字段被使用到.



### ref

与索引进行比较的列



### rows

根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数 



### Extra

额外信息，重要的取值有using filesort, using temporary, 

- **using filesort**

  没有按照创建的索引进行排序，需要额外进行排序

- **using temporary**

- **using index**

  (1)表明相应的select操作中使用了覆盖索引（Covering Index），避免访问了表的数据行，效率不错

  > 覆盖索引：
  >
  > - 建了一个复合索引（c1，c2，c3），查询的时候也是select c1, c2, c3，个数和顺序匹配
  >
  > - 也就是select的数据列只用从索引中就能获取，不用读取数据行

  (2)如果同时出现using where ，表明索引用来执行查找

  (3)如果没有同时出现using where，表明索引用来读取数据

- **using where**

  表明使用了where过滤

- **using join buffer**

  使用了连接缓存

- **impossible where**

  where子句的值总是false，不能用来获取任何行记录

  eg:

  ```sql
  EXPLAIN SELECT * FROM student WHERE `name` = '丘海东' AND `name` = '蔡元俊';
  ```





# 索引优化

1. 最好进行全值匹配

2. 最佳左前缀法则

3. 不在索引列上做任何操作（计算、函数、类型转换等）

4. 范围查询后面列 用不到 索引

5. 尽量使用覆盖索引（索引列和查询列一致），少用select * 

6. MySQL 使用不等于（!= 或者 <>）的时候无法使用索引，会导致全表扫描

7. is null, is not null 无法使用索引

8. like 以通配符开头（'%abc'），索引失效变成全表扫描

   > 如何解决两边%，索引失效？
   >
   > 使用覆盖索引，将字段设置为索引，然后select 该字段，而不使用select *
   >
   > type 由ALL 改进为 index

9. 字符串类型要加单引号，不然mysql会做类型转换，导致索引失效，见上面第3点

10. 少用or，用其会导致索引失效

    