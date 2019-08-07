# 前言

- 开启慢查询日志，可以让MySQL记录下查询超过指定时间的语句，通过定位分析性能的瓶颈，才能更好的优化数据库系统的性能。

- 只有需要调优的时候才开启，开启改功能会影响数据库性能

> 环境
>
> ubuntu 16.04
>
> MySQL版本为5.7
>
> 参考资料：
>
> - 官方文档：<https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html>





# 开始

## 1. 检查慢查日志是否开启

```sql
show variables like 'slow_query_log'
```

补充：查看所有日志变量信息

```sql
show variables like 'slow_query_log'
```

## 2. 开启慢查询日志

- 方法一：一次性有效（如果数据库重启就会失效）

```sql
-- 设置日志路径
set global slow_query_log_file = '/var/lib/mysql/mysql-slow.log';
-- 设置记录查询超过1秒的sql
set global long_query_time=1;
-- 开启
set global slow_query_log='ON';
-- log_queries_not_using_indexes：未使用索引的查询也被记录到慢查询日志中（可选项）。
set global log_queries_not_using_indexes='ON';
```

- 方法二：永久有效，需要修改my.cnf（该文件不同操作系统，或者不同MySQL版本位置不一样）

  在[mysqld]添加以下内容：

```shell
slow_query_log_file = '/var/lib/mysql/mysql-slow.log';
long_query_time = 1;
slow_query_log = 'ON';
```

​	添加之后需要重启mysql服务生效

```shell
service mysql restart
```



## 3. 测试

- 执行语句

```sql
select sleep(2);
```

- 查看日志文件





# 日志格式

```shell
# Time: 2019-05-13T02:24:19.415169Z
# User@Host: root[root] @ localhost []  Id:     3
# Query_time: 2.000450  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 0
SET timestamp=1557714259;
select sleep(2);
```

解释：

- Query_time 语句执行的时间（单位秒）
- Lock_time 获取锁的时间以（单位秒）
- Rows_sent：实际返回的 row 数量
- Rows_examined：实际读取的 row 数量





# 分析工具（mysqldumpslow）

- mysqldumpslow --help 查看使用帮助

- eg:分析出使用频率最高的前50条慢sql：

  ```shell
  mysqldumpslow -s c -t 50 日志路径
  ```





# 查找有问题的sql的思路：

- 执行次数多，执行时间长
- IO次数多，可以通过examine_row的大小来反映
- 索引命中率低，反应在Rows examine 大 Rows Send 小





