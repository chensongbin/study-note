# Spring是什么：

如果单独讲概念的话，直接看百科就行。这里主要列出了Spring的两个核心概念：

- IoC 控制反转
- AOP 面向切面编程



## IoC控制反转

> **IoC(Inversion of Control) 控制反转**

把创建对象的这个工作，由程序员转交给“容器”

当需要某个对象的时候，不是通过new硬编码实现，而是通过容器获取需要的对象

Ioc不仅仅是创建对象，而且还管理着对象的生命周期



> **DI(Dependency Injection) 依赖注入**

Class A中用到了Class B的对象b，一般情况下，需要在A的代码中显式的new一个B的对象。

采用依赖注入技术之后，A的代码只需要定义一个私有的B对象，不需要直接new来获得这个对象，而是通过相关的容器控制程序来将B对象在外部new出来并注入到A类里的引用中。

*什么是依赖：*

*一个类A的实现需要依赖于类B，也就是要用到类B提供的方法*

*因为大多数应用程序都是由两个或是更多的类通过彼此的合作来实现企业逻辑，这使得每个对象都需要获取与其合作的对象（也就是它所依赖的对象）的引用*

为了更好理解DI，我们先来看看通过硬编码来使用依赖：

```java
//Hardcoded dependency
public class MyClass { 
    private MyDependency myDependency = new MyDependency(); 
}
```

再来看看依赖注入的方式：

```java
//Injected dependency
public class MyClass { 
    private MyDependency myDependency;
    public MyClass(MyDependency myDependency){
        this.myDependency = myDependency;
    }
}
```

我们可以有多种方式实现把依赖注入，常见是通过构造器或者setter方法

> **IoC和DI**

通过IoC容器来创建对象，并自动把相关的依赖对象注入进去，我们程序员只需要定义类之间的依赖即可，依赖注入这件事交给IoC容器即可

```java
public class MyClass1 {
    @Autowired	// IoC容器会自动生成好myClass2对象
    private MyClass2 myClass2;
    public void doSomething(){
        myClass2.doSomething();
    }
}
```

```java
public class MyClass2 {
    @Autowired  // IoC容器会自动生成好对象,并将引用赋值给myclass3
    private MyClass3 myClass3;
    @Autowired  // IoC容器会自动生成好对象,并将引用赋值给myclass4
    private MyClass4 myClass4;
    public void doSomething(){
        myClass3.doSomething();
        myClass4.doSomething();
    }
}
```



>个人理解：
>
>一个应用程序一般都是多个类相互合作
>
>（1）传统开发方式：如果类A需要用到类B提供的功能，那么需要在类A中创建类B(也就是new 对象)，如果发生需求变更，也就是现在有了C类，我们发现他能实现和B类一样的功能，但是性能更加出色，于是如果我们想要用C类的功能，那么就需要更改原系统的代码，把那些用到B类的地方，全部换掉。这样很麻烦
>
>（2）现在有了Spring提供的IoC容器，相当于在类A和类B之间加了一个中间层（这里我们称为Container）,由我们的Container利用反射去生成我们需要的对象。现在A类需要B类，那么只需要从Container里面拿。那么如果要用类C替换类B呢，只需要更改Container的配置文件，把B类改为C类，那么容器利用反射，生成的就是C类的对象。这样就可以做到无需要更改源代码。

参考：

<https://baike.baidu.com/item/%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC/1158025?fromtitle=%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5&fromid=5177233>

<https://dzone.com/articles/ioc-vs-di>



## AOP面向切面编程

基本概念：

- 面向对象编程的基本单位是类，而面向切面编程的基本单位是切面

- [具体可以看看这篇博文章：Spring AOP就是这么简单]: https://juejin.im/post/5b06bf2df265da0de2574ee1	"Spring AOP就是这么简单"

底层实现：动态代理

- JDK动态代理，需要实现接口
- Cglib动态代理，通过继承，产生子类

> 个人理解：
>
> （1）比如我们在业务层有多个方法需要开启关闭事务，可以看到以下代码有很多重复代码
>
> ```java
> public void service1(){
>     openTransaction(); // 开启事务
>     // 业务逻辑代码
>     closeTransaction();	// 关闭事务
> }
> public void service2(){
>     openTransaction(); // 开启事务
>     // 业务逻辑代码
>     closeTransaction();	// 关闭事务
> }
> public void service3(){
>     openTransaction(); // 开启事务
>     // 业务逻辑代码
>     closeTransaction();	// 关闭事务
> }
> .....
> ```
>
> （2）采用面向切面编程，我们将进行横向切割， 把不是业务逻辑的代码抽取出来，形成一个模块，单独进行维护
>
> ```java
> class Aspect{
>     openTransaction(){
>         
>     }
>     closeTransaction(){
>         
>     }
> }
> ```
>
> （3）最后 通过动态代理的方式  将**横切出来的逻辑代码**，**融合到业务逻辑**中





# 使用XML管理Bean

## Bean配置方式

将bean交给Spring管理，那么Spring是怎么创建对象的呢？

三种方法：

- 无参构造方法的方式
- 静态工厂实例化的方式:对象通过工厂类的静态方法获得目标对象

- 实例工厂实例化的方式:需要实例化工厂，调用工厂对象的方法获取目标对象

### 1.无参构造方法的方式（常用）

1. 编写类Bean

   ```java
   public class Bean { 
       public Bean() {
           System.out.println("Bean的构造函数");
       } 
   }
   ```

2. 在xml文件中配置

   ```xml
   <!-- 通过无参构造函数 获取Bean对象-->
   <bean id="bean1" class="BeanXML.Bean"/>
   ```

3. 测试

   ```java
   @Test
   public void demo1() {
   	ApplicationContext context = new ClassPathXmlApplicationContext("BeanXML.xml");
       Bean bean = (Bean)context.getBean("bean1");
   }
   ```

### 2.静态工厂实例化的方式

1. 编写工厂类，Bean类同上

   ```java
   /**
    * 静态工厂
    */
   public class BeanFactory {
       static public Bean getBean() {
           System.out.println("通过BeanFactory静态方法获取Bean对象");
           return new Bean();
       }
   }
   ```

2. 在xml文件中进行配置

   ```xml
   <!-- 通过BeanFactory静态方法获取Bean对象 -->
   <bean id="bean2" class="BeanXML.BeanFactory" factory-method="getBean"/>
   ```

### 3.实例工厂实例化的方式

1. 编写工厂类

   ```java
   /**
    * 实例工厂
    */
   public class BeanFactory1 {
       public Bean getBean() {
           System.out.println("先获得BeanFactory1的对象，再调用对象中的方法获得Bean对象");
           return new Bean();
       }
   }
   ```

2. 在xml文件需要配置两个bean标签,一个是工厂类对象，一个是Bean类对象

   ```
   <!-- 先获得BeanFactory1的对象，再调用对象中的方法获得Bean对象 -->
   <bean id="beanFactory1" class="BeanXML.BeanFactory1" />
   <bean id="bean3" class="BeanXML.Bean" factory-bean="beanFactory1" factory-method="getBean"/>
   ```



## 属性注入

### 1. 通过构造方法进行属性注入

*注意：类person需要实现相应的构造函数*

```xml
<bean id="person" class="pojo.Person">
    <constructor-arg name="id" value="1"/>
    <constructor-arg name="name" value="雄"/>
    <constructor-arg name="age" value="2"/>
</bean>
```

### 2.Set方法的属性注入

*注意：类中需要实现相应的setter方法*

```xml
<!-- name表示对象中的属性名 -->
<!-- value注入普通属性 -->
<!-- ref注入对象属性，前提是该对象需要交给IoC容器管理 -->
<bean id="phone" class="pojo.Phone">
    <property name="id" value="1"/>
    <property name="name" value="小米"/>
    <property name="money" value="1999"/>
</bean>
<bean id="person" class="pojo.Person">
    <property name="id" value="1"/>
    <property name="name" value="雄"/>
    <property name="age" value="2"/>
    <property name="phone" ref="phone"/>
</bean>
```

### 3.通过引入p名称空间完成属性的注入

（1）需要引入p名称空间

- 写法：

  - 普通属性        p:属性名=”值”

  - 对象属性        p:属性名-ref=”配置bean标签的id”

（2）配置如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p" <!-- 引入p名称空间 -->
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
<bean id="phone" class="pojo.Phone" p:id="1" p:name="小米" p:money="1999"/>
<bean id="person" class="pojo.Person" p:id="1" p:name="雄" p:age="2" p:phone-ref="phone"/>
</beans>
```

### 4.SpEL的属性注入（Spring3.0以后）

（1）SpEL：Spring Expression Language，Spring的表达式语言

（2）使用见例子：

```xml
<!-- phoneinfo有个name属性还有一个 calculateMonney()方法-->
<bean id="phoneinfo" class="pojo.PhoneInfo">
    <property name="name" value="小米"/>
</bean>
<!-- 通过SpEL 可以调用其他bean的方法和属性 -->    
<bean id="phone" class="pojo.Phone">
    <property name="name" value="#{phoneinfo.name}"/>
    <property name="money" value="#{phoneinfo.calculateMonney()}"/>
</bean>
<!-- 通过SpEL 也可以注入基本类型数据，字符串用单引号 -->  
<bean id="person" class="pojo.Person">
    <property name="name" value="#{'雄'}"/>
    <property name="age" value="#{2}"/>
    <property name="phone" ref="phone"/>
</bean>
```

### 5.集合类型属性注入

```xml
<bean id="collectionBean" class="pojo.CollectionBean">
    <!-- 数组类型 -->
    <property name="array">
        <list>
            <value>1</value>
            <value>2</value>
            <value>3</value>
        </list>
    </property>
    
    <!-- 注入list集合 -->
    <property name="lists">
        <list>
            <value>雄</value>
            <value>俊</value>
            <value>彬</value>
        </list>
    </property>
    
    <!-- 注入set集合 -->
    <property name="set">
        <set>
            <value>肥熊雄</value>
            <value>肥俊</value>
            <value>彬</value>
        </set>
    </property>
    
    <!-- 注入Map集合 -->
    <property name="map">
        <map>
            <entry key="1" value="111"/>
            <entry key="2" value="222"/>
            <entry key="3" value="333"/>
        </map>
    </property>
</bean>
```

## 其他

- 在一个配置文件中引入其他配置文件

```xml
<import resource=""/>
```

- bean标签中的其他属性

  - scope取值：
    - singleton 单例模式（默认）
    - prototype 多例
  - lazy-init取值：
    - true 使用到bean对象时才创建
    - false（默认）IoC容器创建好之后，就创建好所有管理的bean对象

  - init-method ：对象在创建后，执行某个方法
  - destroy-method：

```xml
<bean id="user" class="User" scope="singleton" lazy-init="true" init-method="" destroy-method=""/>
```



# Spring 中IoC注解方式的使用

相关注解：

- @ComponentScan扫描器
- @Configuration表明该类是配置类
- @Component 指定把一个对象加入IOC容器--->@Name也可以实现相同的效果【一般少用】
- @Repository 作用同@Component； 在持久层使用
- @Service 作用同@Component； 在业务逻辑层使用
- @Controller 作用同@Component； 在控制层使用
- @Resource 依赖关系
  - 如果@Resource不指定值，那么就根据类型来找，相同的类型在IOC容器中不能有两个
  - 如果@Resource指定了值，那么就根据名字来找

直接上例子：

（0）配置文件

```xml
<!-- 指定包名，spring就会到包里扫描，需要被Spring管理的对象 -->
<context:component-scan base-package="IoC_annotation"></context:component-scan>
    
<!-- 如果类上没有注解标记被Spring管理，但是需要使用属性注入的相关注解，就设置该注解 -->
<context:annotation-config/> 
```

（1）接口

```java
public interface UserDao {
    void save();
}
```

```java
public interface UserService {
    void save();
}
```

（2）实现类

```java
@Component("userDao") // 相当于在xml中配置bean标签  其中id为"userDao"
// @Component修饰一个类，将这个类交给Spring管理
public class UserDaoImp implements UserDao {
    
    @Value("小熊")    // 设置属性值
    private String name;
    
    private int age;
    @Value("100")   // 也可以在setter方法使用注解设置属性的值
    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public void save() {
       System.out.println("UserDaoImp---save() name="+name + " age="+age);

    }
}
```

```java
@Service("userService")
public class UserServiceImp implements UserService{

    // 注入UserDao
    //@Autowired  // 这种方式Spring自动去找符合类型的对象，该对象必须被Spring管理
    //private UserDao userDao;
    
    @Resource(name="userDao") // 按名称查找对象并注入
    private UserDao userDao;
    
    @Override
    public void save() {
        userDao.save();
        System.out.println("UserServiceImp.save()");
    }
}
```



# Spring AOP  利用注解方式 

1. **编写目标类（需要被增强的类，也就是被代理的类）**

   ```java
   // 如果不使用接口，Spring底层会使用Cglib动态代理
   public class OrderDao {
       public void save() {
           System.out.println("保存订单");
       }
       public void delete() {
           System.out.println("删除订单");
       }
       public void update() {
           System.out.println("更新订单");
       }
       public void find() {
           System.out.println("查找订单");
           int a = 1/0; // 为了抛出异常
           System.out.println(a);
       }
   }
   ```

2. **配置文件中配置目标类**

   ```xml
    <!-- 配置目标对象 -->
    <bean id="orderDao" class="aop_annotation.OrderDao"/>
   ```

3. **编写切面类**

   ```java
   public class MyAspectAnnotation {
      
   }
   ```

4. **配置文件中配置切面类**

   ```xml
   <!-- 配置切面类 -->
   <bean id="myAspect" class="aop_annotation.MyAspectAnnotation"/>
   ```

5. **在配置文件中开启注解的AOP开发**

   ```xml
   <aop:aspectj-autoproxy/>   
   ```

6. **在切面类上使用注解**

   ```java
   @Aspect
   public class MyAspectAnnotation {
       // 前置通知
       @Before(value="execution(* aop_annotation.OrderDao.save(..))")
       public void before() {
           System.out.println("前置增强.....");
       }
       
       // 后置通知
       @AfterReturning(value="execution(* aop_annotation.OrderDao.delete(..))",returning="result")
       public void after(Object result) {
           System.out.println("后置增强。。。" + result);
       }
       
       // 环绕通知
       @Around(value="execution(* aop_annotation.OrderDao.update(..))")
       public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
           System.out.println("环绕增强前======");
           Object proceed = joinPoint.proceed();
           System.out.println("环绕增强后======");
           return proceed;
       }
       
       // 异常抛出通知
       @AfterThrowing(value="execution(* aop_annotation.OrderDao.find(..))", throwing="e")
       public void afterThrowing(Throwable e) {
           System.out.println("#####异常抛出增强" + e.getMessage());
       }
       
       // 最终通知
       @After(value="execution(* aop_annotation.OrderDao.find(..))")
       public void finalNotice() {
           System.out.println("最终通知*********");
       }
   }
   ```

   *注意：如果切入点更改，那么需要改很多处地方。比如OderDao里面的find方法改名了（见下面的代码），改成了search，那么下面代码中的注解都要进行更改*

   ```java
   // 异常抛出通知
   @AfterThrowing(value="execution(* aop_annotation.OrderDao.find(..))", throwing="e")
   // 最终通知
   @After(value="execution(* aop_annotation.OrderDao.find(..))")  
   ```

   *可以使用注解配置切入点，解决上面的问题*

   ```java
   // 异常抛出通知
   //注意切入点写法:类名.方法名()
   @AfterThrowing(value="MyAspectAnnotation.pointCnt1()", throwing="e")
   public void afterThrowing(Throwable e) {
       System.out.println("#####异常抛出增强" + e.getMessage());
    }
   
   // 最终通知
   //注意切入点写法:类名.方法名()
   @After(value="MyAspectAnnotation.pointCnt1()")  
   public void finalNotice() {
       System.out.println("最终通知*********");
   }
   
   @Pointcut(value="execution(* aop_annotation.OrderDao.find(..))")
   private void pointCnt1() {}
   ```

7. **测试**

   ```java
   /**
    * @RunWith(SpringJUnit4ClassRunner.class)
    * @ContextConfiguration("classpath:aopAnnotation.xml") 配置文件路径
    * 这两个注解是spring整合JUnit
    */
   @RunWith(SpringJUnit4ClassRunner.class)
   @ContextConfiguration("classpath:aopAnnotation.xml")
   public class Demo {
       @Resource(name="orderDao")
       private OrderDao orderDao;
           
       @Test
       public void demo() {
           orderDao.save();
           orderDao.delete();
           orderDao.update();
           orderDao.find();
       }
   }
   /* 运行结果：
   前置增强.....
   保存订单
   删除订单
   后置增强。。。操作员：小雄
   环绕增强前======
   更新订单
   环绕增强后======
   查找订单
   最终通知
   #####异常抛出增强/ by zero
   */
   ```




# Spring AOP xml方式

1. 将需要被增强的对象交给IoC容器管理

   ```java
   public class ProductDaoImp implements ProductDao {
   
       @Override
       public void save() {
           System.out.println("保存商品");
       }
   
       @Override
       public void update() {
           System.out.println("更新商品");
       }
   
       @Override
       public void find() {
           System.out.println("查找商品");
       }
   
       @Override
       public void delete() {
              System.out.println("删除商品");
       }
   }
   ```

   ```xml
    <!-- 配置被增强的对象 -->
     <bean id="productDao" class="aop.ProductDaoImp"/>
   ```

2. 编写切面类，并将切面类交给IoC容器管理

   ```java
   /**
    * 切面类
    */
   public class MyAspectXML {
       /**
        * 前置通知
        * @param joinPoint
        */
       // 可以通过参数里面设置 Joinpoint joinPoint 传入切入点信息
       // 切入点就是，我们要增强的方法
       public void checkPri(JoinPoint joinPoint) {
           System.out.println("权限检测-----" + joinPoint);
       }
       /**
        * 后置通知
        */
       // 可以通过后置通知获取切入点返回的值
       public void writeLog() {
           System.out.println("日志记录--------");
       }
       /**
        * 环绕通知
        * @throws Throwable 
        */
       public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
           System.out.println("环绕前通知-----");
           Object object = joinPoint.proceed();
           System.out.println("环绕后通知-----");
           return object;
       }   
   }
   ```

   ```xml
    <!-- 将切面交给Spring管理 -->
    <bean id="myAspect" class="aop.MyAspectXML"/>
   ```

3. 在xml中进行aop配置

   ```xml
   
     <!-- 通过AOP配置，完成对目标类产生代理 -->
     <!-- expression 配置哪些类的哪些方法需要增强  -->
     <aop:config>
       <!-- 切面 -->
       <aop:aspect ref="myAspect">
           <!-- 切入点 -->
           <aop:pointcut expression="execution(* aop.ProductDaoImp.save(..))" id="pointcut1"/> 
           <!-- 前置通知 --> 
           <aop:before method="checkPri" pointcut-ref="pointcut1"/>
           
           <!-- 切入点 -->
           <aop:pointcut expression="execution(* aop.ProductDaoImp.update(..))" id="pointcut2"/>
           <!-- 后置通知 -->
           <!-- 如果后置通知需要获取返回值，那么需要在 method里设置参数Object retVal -->
           <aop:after-returning method="writeLog" pointcut-ref="pointcut2" returning="retVal"/>
           
           <!-- 切入点 -->
           <aop:pointcut expression="execution(* aop.ProductDaoImp.find(..))" id="pointcut3"/>
           <!-- 环绕通知 -->
           <aop:around method="around" pointcut-ref="pointcut3" /> 
           
       </aop:a spect>
     </aop:config>
   ```

   


# Spring的JDBC模板

## 使用

```java
/**
 * Spring JDBC模板的使用
 * @author Saber
 *
 */

public class JDBCDemo1 {
    
    @Test
    // JDBC模板的使用类似JbUtil
    public void demo1() {
        // 创建连接池
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUsername("root");
        dataSource.setPassword("123456");
        dataSource.setUrl("jdbc:mysql:///aa");
        // 创建jdbc模板
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        jdbcTemplate.update("insert into person values (null, ?, ?)", "雄", 10);
    }
}
```

我们观察上面的代码发现：DriverManagerDataSource和JdbcTemplate这两个类需要我们自己创建，我们可以将他们交给Spring容器管理

(1)配置文件 spring_jdbc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns = "http://www.springframework.org/schema/beans"
   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance" 
   xmlns:aop = "http://www.springframework.org/schema/aop"
   xsi:schemaLocation = "
   http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd">
    
  <!--   将Spring内置的JDBC连接池(org.springframework.jdbc.datasource.DriverManagerDataSource)交给Spring管理 -->
   <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!-- 
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUsername("root");
        dataSource.setPassword("123456");
        dataSource.setUrl("jdbc:mysql:///aa");
                    依赖注入
         -->
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
        <property name="url" value="jdbc:mysql:///aa"/>
   </bean>
   
   <!-- 将org.springframework.jdbc.core.JdbcTemplate交由Spring容器管理 -->
   <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
   </bean>
</beans>
```

(2)测试代码

```java
@Test
// 使用IoC修改demo1
public void demo2() {
	ApplicationContext context = new ClassPathXmlApplicationContext("spring_jdbc.xml");
	// 从Spring容器中获取jdbc模板对象
	JdbcTemplate jdbcTemplate = (JdbcTemplate)context.getBean("jdbcTemplate");
	jdbcTemplate.update("insert into person values (null, ?, ?)", "肥雄", 100);
}
```

## 使用第三方连接池

> 常用第三方连接池有dbcp 和 c3p0，下面以dbcp为例子

1. 引入jar包
2. 配置文件中配置

```xml
<!-- 如果需要使用dpcp连接池  -->
<bean id="dbcpDataSource" class="org.apache.commons.dbcp.BasicDataSource">
	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	<property name="username" value="root"/>
	<property name="password" value="123456"/>
	<property name="url" value="jdbc:mysql:///aa"/>
</bean>

<!-- 将org.springframework.jdbc.core.JdbcTemplate交由Spring容器管理 -->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
	<property name="dataSource" ref="dbcpDataSource"/>
</bean>
```

3. 测试代码

```java
@Test
public void demo2() {
	ApplicationContext context = new ClassPathXmlApplicationContext("spring_jdbc.xml");
	// 从Spring容器中获取jdbc模板对象
	JdbcTemplate jdbcTemplate = (JdbcTemplate)context.getBean("jdbcTemplate");
	jdbcTemplate.update("insert into person values (null, ?, ?)", "大肥雄", 1000);
}
```

**观察上方配置文件中的对连接池对象的属性注入，如果配置有多个连接池，如果要对username这个属性进行更改，那么需要改很多处地方。**

解决办法：引入外部属性文件

1. 创建一个属性文件 jdbc.properties

   ```xml
   jdbc.driverClass=com.mysql.jdbc.Driver
   jdbc.url=jdbc:mysql:///aa
   jdbc.username=root
   jdbc.password=123456
   ```

2. 在spring配置文件中引入属性文件

   ```xml
      <!-- 引入属性文件================================== -->
       <!-- 第一种方式通过一个bean标签引入的（很少） -->
   <!--    
       <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
           <property name="location" value="classpath:jdbc.properties"/>
       </bean> 
   -->
       
       <!-- 第二种方式通过context标签引入的 -->
      <context:property-placeholder location="classpath:jdbc.properties"/>
   ```

3. 通过${key} 就可以获取属性文件中的value值

   ```xml
   <!-- 配置C3P0连接池=============================== -->
   <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
   	<property name="driverClass" value="${jdbc.driverClass}"/>
   	<property name="jdbcUrl" value="${jdbc.url}"/> <!-- 注意C3p0的url有些不同 -->
   	<property name="user" value="${jdbc.username}"/>
   	<property name="password" value="${jdbc.password}"/>
   </bean>
   ```


## CRUD操作

0. 创建数据库表

   ```java
   CREATE TABLE `person` (
     `id` BIGINT PRIMARY KEY AUTO_INCREMENT,
     `name` VARCHAR(50),
     `age` INT
   )
   ```

   整合JUnit 和 Spring，方便进行测试

   ```java
   @RunWith(SpringJUnit4ClassRunner.class)
   @ContextConfiguration("classpath:spring_jdbc.xml")
   public class CRUD {
       @Resource(name="jdbcTemplate")
       private JdbcTemplate jdbcTemplate;
       
       @Test
       public void demo() {
           // 直接使用jdbcTemplate进行测试
       } 
   }
   
   ```

1. 保存操作

   ```java
   @Test
   // 保存操作
   public void demo1() {
   	jdbcTemplate.update("insert into person values(null, ?, ?)", "西伯利亚雄", 2);
   }
   ```

2. 修改操作

   ```java
   @Test
   // 修改操作
   public void demo2() {
   	jdbcTemplate.update("update person set name = ? where id = ?", "希伯里", 1);
   }
   ```

3. 删除操作

   ```java
   @Test
   // 删除操作
   public void demo3(){
       jdbcTemplate.update("delete from person where id = ?", 1);
   }
   ```

4. 查询某个字段，并把结果进行封装成相应的对象

   ```java
   @Test
   // 查询操作
   public void demo4(){
       Long count = jdbcTemplate.queryForObject("select count(*) from person", Long.class);
       System.out.println(count);
   }
   ```

5. 查询一条记录，将结果封装到实体类中，需要实现RowMapper<T>接口，将结果集的字段与对象属性对应

   ```java
   @Test
   // 封装到一个对象中
   public void demo5(){
       // 自定义的RowMapper，具有一定的通用性
       MyRowMapper<Person> rowMapper = new MyRowMapper<>(Person.class);
       Person person = (Person) jdbcTemplate.queryForObject("select * from person where id = ?", rowMapper,2);
       System.out.println(person);
   }
   ```

   **RowMapper<T> 通用实现，利用反射**

   ```java
   public class MyRowMapper<T> implements RowMapper<T>{
       private Class<?> targetClazz;
       
       // 保存属性名与Field对象的映射，方便从数据库字段名定位Field对象
       private Map<String, Field> fieldMap;
       
       // 构造方法
       public MyRowMapper(Class<?> targetClazz){
          this.targetClazz = targetClazz;
          
          fieldMap = new HashMap<String, Field>();
          // 获取类的全部属性，包括私有属性
          Field[] declaredFields = this.targetClazz.getDeclaredFields();
          for(int i=0;i<declaredFields.length;++i) {
              // 存储时，将属性名称全部化为小写，方便比较
              String name = declaredFields[i].getName().toLowerCase();
              fieldMap.put(name, declaredFields[i]);
          }
       }
       
       @Override
       public T mapRow(ResultSet rs, int rowNumber) throws SQLException {
           ResultSetMetaData metaData = rs.getMetaData();
           int columnCount = metaData.getColumnCount();
               
           Object obj = null;
           try {
               obj = targetClazz.newInstance();
               for(int i=0;i<columnCount;++i) {
                   String colName = metaData.getColumnName(i+1);// ColumnName下标从1开始
                   // 去除数据库字段名的下划线
                   colName = colName.replaceAll("_", "");
                   // 转化为小写
                   colName = colName.toLowerCase();
                   Field field = fieldMap.get(colName);
                   field.setAccessible(true);
                   field.set(obj, rs.getObject(i+1));  // 下标从1开始
                   field.setAccessible(false);
               }
           } catch (InstantiationException | IllegalAccessException e) {
               e.printStackTrace();
           }
           return (T)obj;
       }
   }
   ```

6. 查询多条记录

   ```java
   @Test
   // 查询多条记录
   public void demo6() {
       MyRowMapper<Person> rowMapper = new MyRowMapper<>(Person.class);
       List<Person> persons = jdbcTemplate.query("select * from person", rowMapper);
       System.out.println(persons);
   }
   ```



