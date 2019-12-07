- MySQL数据库插入值出现问号

  检查点：

  - show variables like 'character_set%'

  - 建立连接的url末尾加上?useUnicode=true&characterEncoding=utf-8

    ```java
    jdbc:mysql://192.168.129.10:3307/mybatis?useUnicode=true&characterEncoding=utf-8
    ```



- MySQL  datetime类型对零值的支持

  Data truncation: Incorrect datetime value: '0000-00-00 00:00:00' for column xxx

  5.7.21  支持 datetime 默认值为'0000-00-00 00:00:00' 
  5.7.22 不支持

  https://blog.csdn.net/qq_33581278/article/details/88958198



- 

