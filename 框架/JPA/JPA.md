



#JPA使用

创建persistence.xml

- 指定跟哪个数据库交互
- 指定JPA使用哪个持久化框架以及配置该框架的基本属性

创建实体类，使用注解描述实体类跟数据库表的关系

使用JPA API完成增删改查

- 创建EntityManagerFactory
- 创建EntityManager





#JPA注解

@Entity

@Table

@Column





#JPA api

- Persistence 用来创建EntityManagerFactory
- EntityManagerFactory 





#表与表的关联关系

- 一对多

  @OneToMany

  @JoinColumn 外键列的名称

  

- 多对一

  ​	



# 其他

- mysql 中的 for update

  



