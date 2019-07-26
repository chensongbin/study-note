关键词：

AnnotationConfigApplicationContext

@Configuration 标志该类为配置类

@Bean 注册一个bean

@ComponentScan 包扫描

@Scope  默认单例singleton  

@Conditional 按条件注册bean



#@Conditional 按条件注册bean

- 自定义类实现Condition接口

```java
public interface Condition {
	/**
	 * context 
	 * metadata 所有注解信息
	 * 返回 true 则注册到容器中， 否则不注册到容器
	 * 
	 */
	boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);
}
```

- 注解@Conditional标注需要按条件注册到容器的bean

Spring会调用matches方法，根据返回值决定是否将该bean注册到容器中





# 给Spring IOC容器中注册组件的方式

- 包扫描 + 组件标注注解（@Controller, @Service, @Repository, @Component），适合导入自己写的组件
- @Bean 适合导入第三方包的组件
- @import ：id默认是全类名
  - 导入自定义类（实现ImportSelector接口），返回需要导入的组件的全类名数组
  - 导入自定义类（实现ImportBeanDefinitionRegistrar），使用BeanDefinitionRegistrar注册组件

- 使用Spring提供的FactoryBean(工厂bean)

  - 通过bean id获取到的对象是FactoryBean中getObject()方法创建的对象

  - 如果要获取工厂bean本身，获取bean时id前要加&

    

    

# Bean生命周期

- 构造方法 ---> initMethod ---> destroyMethod

- 方式一：

  ```java
  @Bean(initMethod = "init", destroyMethod = "destory")
  ```

- 方式二：

  通过让bean实现InitializingBean（定义初始化逻辑），DisposableBean（定义销毁逻辑）

- 方式三：

  在初始化方法上添加注解@PostConstruct

  在销毁方法上添加注解@PreDestroy

- BeanPostProcessor（接口）后置处理器（在初始化前后添加代码逻辑）

  然后将该实现类添加到Spring容器中

  ```java
  /**
  BeanPostProcessor的实现
  */
  doCreateBean() {
      // (1)???
      populateBean()  
      // 调用initializeBean方法
      initializeBean(){
          // (2)遍历所有的BeanPostProcessor并调用	postProcessBeforeInitialization()方法
      	applyBeanPostProcessorsBeforeInitialization()
      	// (3)调用bean初始化方法
  		invokeInitMethods() 
      	// (4)遍历所有的BeanPostProcessor并调用postProcessAfterInitialization()方法
  		applyBeanPostProcessorsAfterInitialization()
      }
  }
  ```

  注意：

  Spring底层对BeanPostProcessor的使用：

  （1）bean赋值

  （2）注入其他组件

  （3）@Autowired

  （4）生命周期注解@PostConstruct @PreDestroy

  

# 属性赋值

## @Value

- 基本类型

- SpEL : #{20-3}

- ${} ：取出配置文件的值（在运行环境变量里面的值）

  需要使用@PropertySource读取外部配置文件中的k/v保存到运行的环境变量中

  

# 自动装配

## @Autowired

1. 默认按照类型去容器中找对应的bean

2. 如果类型由多个实例，将属性名作为id去容器中找

3. @Qualifier 明确指定装配的bean

4. 如果@Autowired在容器中找不到合适的实例，就会报错

   可以使用@Autowired(required=false)解决

5. @Primary 让Spring进行自动专配的时候，默认使用首选的bean，也可以继续使用@Qualifier指定需要装配的bean的id

6. @Autowired可以修饰：

   - 构造器
   - 方法参数
   - 方法
   - 属性

   都是从容器中获取需要的对象



## @Resource (JSR250 属于Java规范)

1. 和@Autowired一样可以实现自动装配

2. 不支持Spring中的注解@Primary



##  @Inject (JSR330)

1.  需要导入javax.inject的包
2. 和@Autowired功能差不多



## 注入Spring底层的组件

自定义类实现XXXAware接口，在创建对象的时候，会调用接口的方法将相关组件注入进自定义类

eg: 

- ApplicationContextAware接口，注入ioc容器
- BeanNameAware接口，注入bean 的 id
- EmbeddedValueResolverAware接口，注入StringValueResolver，可以解析#{} , ${}

*实现：以ApplicationContextAware接口为例*

创建完相应的bean之后，会调用ApplicationContextAwareProcessor中的postProcessBeforeInitialization(final Object bean, String beanName)方法，该方法会调用bean中的setApplicationContext()方法



  ## 补充
自动装配功能是如何实现的？

AutowiredAnnotationBeanPostProcessor



# @Profile根据指定环境加载组件

- 使用：

  将指定的bean用@Profile标注

- 切换环境方式一：

运行时虚拟及参数：

```java
-Dspring.profiles.active=test
```

- 切换环境方式二：

```java
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();       context.getEnvironment().setActiveProfiles("test");
context.register(MainConfigOfProfile.class);
context.refresh();
```





# Spring注解方式实现AOP

## **AOP：**

- 将与业务逻辑无关的代码切出来，单独封装成一个类进行维护
- 指定将其加入的位置，在运行时通过动态代理将代码加进去。

相关概念的理解：<https://blog.csdn.net/troubleshooter/article/details/78467637>

> a. **连接点（Joinpoint）**：程序执行的某个特定位置（如：某个方法调用前、调用后，方法抛出异常后）。一个类或一段程序代码拥有一些具有边界性质的特定点，这些代码中的特定点就是连接点。Spring仅支持方法的连接点。 
> b. **切点（Pointcut）**：如果连接点相当于数据中的记录，那么切点相当于查询条件，一个切点可以匹配多个连接点。Spring AOP的规则解析引擎负责解析切点所设定的查询条件，找到对应的连接点。 
> c. **增强（Advice）**：增强是织入到目标类连接点上的一段程序代码。Spring提供的增强接口都是带方位名的，比如：BeforeAdvice、AfterReturningAdvice、ThrowsAdvice等。很多资料上将增强译为“通知”，这明显是个词不达意的翻译，让很多程序员困惑了许久。
> d. **引介（Introduction）**：引介是一种特殊的增强，它为类添加一些属性和方法。这样，即使一个业务类原本没有实现某个接口，通过引介功能，可以动态的为该业务类添加接口的实现逻辑，让业务类成为这个接口的实现类。 
> e. **织入（Weaving）**：织入是将增强添加到目标类具体连接点上的过程，AOP有三种织入方式：①编译期织入：需要特殊的Java编译器（例如AspectJ的ajc）；②装载期织入：要求使用特殊的类加载器，在装载类的时候对类进行增强；③运行时织入：在运行时为目标类生成代理实现增强。Spring采用了动态代理的方式实现了运行时织入，而AspectJ采用了编译期织入和装载期织入的方式。 
> f. **切面（Aspect）**：切面是由切点和增强（引介）组成的，它包括了对横切关注功能的定义，也包括了对连接点的定义。



## **使用步骤**

1. 导入AOP模块
2. 定义业务逻辑类
3. 定义切面类
4. 给切面类的目标方法标注何时何地运行
5. 将切面类和业务逻辑类加入容器
6. 告诉容器哪一个是切面类（使用@Aspect）
7. 配置类中加上@EnableAspectJAutoProxy



## AOP原理

1. 创建IOC容器

2. 因为有@EnableAspectJAutoProxy

   会往容器中注册**AnnotationAwareAspectJAutoProxyCreator**对象

   - 该对象的父类实现了SmartInstantiationAwareBeanPostProcessor（因此该对象是一个后置处理器，spring在创建类的时候会调用该对象）
   - 该对象的父类还实现了BeanFactoryAware（因此初始化时候会将BeanFactory注入该对象）

3. 注册其他bean（包括业务逻辑类和切面类）到Spring容器中

   在将这些bean加入容器中，会调用所有的后置处理器（包括步骤2中的后置处理器）在初始化前后执行相关操作

   当调用到**AnnotationAwareAspectJAutoProxyCreator** 这个后置处理器时，在这个过程中，会判断该类是否需要增强，如果需要就使用jkd动态代理或者Cglib动态代理，将增强的对象加入到Spring容器中

4. 实际执行目标方法，调用的代理对象的方法

   

