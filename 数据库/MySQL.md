## case when语句



## 数据类型
1. utf8 一个汉字三个字节
2. gbk 一个汉字2个字节
3. varchar(n)表示n个字符，不是n个字节
4. MySQL查看字段长度
```sql
select LENGTH(user_name) from user;
```
5. float(m, n) m表示总位数，n表示小数位数
  如果字段为float(5,2)类型
  插入12345  出错 原因小数部分占了2位，因此整数最大只能为3位
  插入123.124 成功，显示为123.12（小数部分四舍五入）
6. 字符串
- char(n) 
- varchar(n)
7. 日期时间类型
- date 日期‘2018-12-12’
- time 时间 '12:25:11'
- datetime 日期时间‘2018-12-12 22:00:00’
- timestamp 时间戳
  datetime与timestamp的区别
  （1）mysql在存储timestamp类型时会将时间转为UTC时间，然后读取的时候再恢复成当前时区。
  （2）timestamp由于受32位int型的限制，能表示'1970-01-01 00:00:01'到'2038-01-19 03:14:07'的UTC时间。

## MySQL数据类型的属性
1. NULL
2. NOT NULL
3. DEFAULT 默认值
4. PRIMARY KEY 主键
5. AUTO_INCREMENT 自增
6. UNSIGNED 无符号
7. CHARACTER SET name
8. COMMENT '注释'

## like子句
like如果不用%,效果等同于=（等于号）
```sql
select * from user where user_name lile %明%
```

## MySQL元数据
### 获取服务器元数据
- select version();
- select database(); 返回当前use数据库
- select user(); 返回当前用户
- show status 服务器状态
- show variables 服务器配置变量


## alter命令
- 删除表字段
```sql
-- user 是表名，money字段名
alter table user drop money;
```
- 添加表字段
```sql
alter table user add money float(5,3);
```
- 修改表字段
   change和modify区别
   修改字段名称只能是用change，modify不能修改字段名称
```sql
-- 需求：修改user表胡name字段
-- 使用modify
 alter table user modify name varchar(20) not null default '无名氏' comment '名字';
 -- 使用 change 可以改字段名
 alter table user change name new_name varchar(20) not null default '无名氏' comment '名字';
````
- 修改表名
```sql
alter table user rename to user_table;
```

## MySQL函数
### 字符串函数
- 
### 

## 其他
- 查看建表语句
```sql 
-- user 是表名
show create table user;
```