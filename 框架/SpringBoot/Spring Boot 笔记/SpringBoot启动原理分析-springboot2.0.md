基于SpringBoot2进行分析



# 先列出关键的类

**1、SpringApplication实例化时**

- SpringApplication
- **ApplicationContextInitializer** （从spring.factories中获取）
- ApplicationListener（从spring.factories中获取）

**2、实例化后，调用run方法**

- **SpringApplicationRunListener** （从spring.factories中获取）

- ApplicationContext （ioc容器）

- **ApplicationRunner** 
- **CommandLineRunner** 



# 步骤

## 一、创建SpringApplication对象



## 二、调用二中对象的run方法

1. 获取所有的SpringApplicationRunListener，并调用starting()方法

   

2. 创建环境

   - 环境创建完后回调SpringApplicationRunListener的environmentPrepared方法

   

3. 创建ioc容器

   

4. prepareContext() 方法，准备ioc容器

   - ApplicationContextInitializer的initialize方法

   - SpringApplicationRunListener的contextPrepared方法

   - SpringApplicationRunListener的contextLoaded方法

   

5. refreshContext(context) 刷新ioc容器，ioc容器初始化，扫描，创建组件

   - 会解析@import中指定的组件

   

6. listeners.started(context);

   - 调用SpringApplicationRunListener的started()方法

     

7. callRunners(context, applicationArguments);从ioc容器中获取所有ApplicationRunner和CommandLineRunner

   - 调用ApplicationRunner的run方法

   - 调用CommandLineRunner的run方法

     

8. listeners.running(context);

   - 调用SpringApplicationRunListener的**running**方法



