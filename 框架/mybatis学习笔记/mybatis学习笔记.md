

# 是什么

## 传统JDBC开发存在的问题

- 频繁创建释放连接消耗大（可以通过数据库连接池解决）

- 需要重复写创建连接与释放连接的代码
- sql语句内置在代码中，不好维护
- 需要自己手动封装数据库查询的结果



## 什么是Mybatis



 



##mybatis架构

 





mybatis 接口遵循的四个原则

- 接口方法名 == xml文件中的id
- 返回值类型与xml文件中的一致
- 方法的入参类型 与 xml文件中的一致
- xml中的namespace 与 接口全名一致





# 动态sql标签

- where标签，检测并去掉前面的and

- sql标签，提取公用的sql片段

- foreach标签

  属性

  - collection
  - item
  - separator
  - open
  - close



#模糊查询LIKE的三种方式

<https://blog.csdn.net/why15732625998/article/details/79081146>



