# 大纲

- 内置三个注解

  - @Override 
  - @Deprecated
  - @SuppressWarnings     

- 四个元注解

  - @Target

  - @Retention

  - @Document

  - @Inherited

  - Java8中新增元注解@Repeatable

    

- 自定义注解

  - 成员变量元素类型
    - 八种基本数据类型
    - String、Class、Enum、Annotation
    - 以上类型的数组

  - 成员变量可以设置默认值
  - 注解在只给元素value赋值时，在使用注解的时候可以省略“value=”，直接写需要的值即可
  - 自定义注解如果没有使用
    - 没有使用@Target 则此注解可以用于任何元素之上
    - 没有使用@Retention 默认是RetentionPolicy.CLASS







# 参考资料

深入理解Java注解类型<https://blog.csdn.net/javazejian/article/details/71860633>

