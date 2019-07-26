# Spring 重要类

- **BeanDefinition**  bean的定义信息，可以理解为bean标签对应的javabean
- **BeanDefinitionRegistry**  BeanDefinitionRegistry接口提供了向容器注册，删除，获取BeanDefinition对象的方法



- BeanPostProcessor 与 BeanFactoryPostProcessor 区别

  <https://www.cnblogs.com/dreampig/p/9036077.html>





从AnnotationConfigApplicationContext实例化开始

```
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ConfigDemo.class);
```

1. AnnotationConfigApplicationContext的构造方法
   1. 调用无参构造函数
      1. this.reader = new AnnotatedBeanDefinitionReader(this); //注册 处理注解相关的处理器的BeanDefinition（注意此时还未实例化对象到容器中）
      2. this.scanner = new ClassPathBeanDefinitionScanner(this);//提供自动扫描功能的核心类
2. register(配置类)
3. refresh









# @EnableConfigurationProperties

1. ```java
   @Import(EnableConfigurationPropertiesImportSelector.class)
   public @interface EnableConfigurationProperties 
   {
   	。。。省略
   }
   ```

2.  这个注解本身又被@Import(EnableConfigurationPropertiesImportSelector.class)注解了，被导入的EnableConfigurationPropertiesImportSelector是一个导入选择器，这个类的作用是导入“ConfigurationPropertiesBeanRegistrar.class”和“ConfigurationPropertiesBindingPostProcessorRegistrar.class”，被导入的这两个类有一个共同的特点，它们都实现了ImportBeanDefinitionRegistrar接口，也就是说被导入的这两个类本身的作用是往bean工厂注册bean定义对象的。

3. （1）ConfigurationPropertiesBindingPostProcessorRegistrar：负责注册ConfigurationPropertiesBindingPostProcessor，该后置处理器的作用时绑定bean的配置属性值

   （2）ConfigurationPropertiesBeanRegistrar ： 负责注册配置类依赖的被@ConfigurationProperties注解的类，比如DispatcherServletConfiguration依赖WebMvcProperties，于是乎WebMvcProperties也被注册到bean工厂了。

