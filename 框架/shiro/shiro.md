# 前言

本demo使用的shiro版本为1.4.1

代码：



# 是什么：

Apache Shiro 是 Java  的一个安全（权限）框架。 



主要功能：







# 架构

**从外部来看**：

应用程序直接使用Subject

SecurityManager 核心组件

Realm 与数据打交道

![ShiroBasicArchitecture](image/ShiroBasicArchitecture.png)

**从内部看**



![ShiroArchitecture](image/ShiroArchitecture.png)

**Subject** 

**SecurityManager**

**Realms** 





# 认证（login）

**步骤：**

```java
1、获取当前Subject
SecurityUtils.getSubject() 

2、判断是否登录
Subject.isAuthenticated()

3、如果没有登录，封装用户信息，执行登录
Subject.login()

4、自定义实现Realm接口

5、shiro根据从Realm获取的数据完成认证
```

**Realm的作用**

```java
（1）首先需要获得用户输入的信息
AuthenticationToken token

（2）Realm则
通过token的获取数据库信息并封装成 
AuthenticationInfo info

（3）shiro就会比对（1）（2）中的信息进行判断
```

**密码的比对**

通过AuthenticatingRealm的 credentialsMatcher



## 密码加密

由于密码的比对是通过AuthenticatingRealm的 **credentialsMatcher** 进行比对的

```java
// CredentialsMatcher是一个接口
public interface CredentialsMatcher {
    boolean doCredentialsMatch(AuthenticationToken token, AuthenticationInfo info);
}
```

因此直接替换CredentialsMatcher的实现类

```xml
<bean id="jdbcRealm" class="com.example.shiro.reamlms.ShiroRealm">
    <property name="credentialsMatcher">
        <bean class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
            <!--加密算法 MD5、SHA1-->
            <property name="hashAlgorithmName" value="SHA1"></property>
            <!--加密次数-->
            <property name="hashIterations" value="1024"></property>
        </bean>
    </property>
</bean>
```



实际执行加密算法使用的是

```java
org.apache.shiro.crypto.hash.SimpleHash
```



为什么要加盐?

如果有两个用户的密码相同，那么哈希算法之后的值一样，这样存在数据库里面不怎么好

同时加盐加大了破解密码的难度



## 多Realm

**步骤**

（1）新增一个Realm的实现（secondRealm）

（2）加入Spring容器中

```xml
<bean id="secondRealm" class="com.example.shiro.realms.SecondRealm">
    <property name="credentialsMatcher">
        <bean class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
            <property name="hashAlgorithmName" value="SHA1"></property>
            <property name="hashIterations" value="1024"></property>
        </bean>
    </property>
</bean>
```

（3）Spring容器中加入认证器 (ModularRealmAuthenticator)

```xml
<bean id="authenticator" 
    	class="org.apache.shiro.authc.pam.ModularRealmAuthenticator">
    <property name="realms">
        <list>
            <ref bean="jdbcRealm"/>
            <ref bean="secondRealm"/>
        </list>
    </property>
</bean>
```

（4）修改securityManager的配置，配置认证器为（3）中定义的认证器

```xml
 <bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
 	<property name="authenticator" ref="authenticator"/> 
 </bean>
```



**认证策略(AuthenticationStrategy ):**

1. 是什么:

   规定 有多个realm时，怎样才能认证成功

2. AuthenticationStrategy 接口的默认实现： 

   - FirstSuccessfulStrategy：只要有一个 Realm 验证成功即可，只返回第 一个 Realm 身份验证成功的认证信息，其他的忽略； 
   - AtLeastOneSuccessfulStrategy：只要有一个Realm验证成功即可，和 FirstSuccessfulStrategy 不同，将返回所有Realm身份验证成功的认证信 息
   - AllSuccessfulStrategy：所有Realm验证成功才算成功，且返回所有 Realm身份验证成功的认证信息，如果有一个失败就失败了。 

3. ModularRealmAuthenticator 默认是 AtLeastOneSuccessfulStrategy 策略

4. 如何配置，在认证器中指定

   ```xml
    <bean id="authenticator" 
       	class="org.apache.shiro.authc.pam.ModularRealmAuthenticator">
       	<property name="authenticationStrategy">
               <!-- 
   			(1)FirstSuccessfulStrategy
   			(2)AtLeastOneSuccessfulStrategy默认
   			(3)AllSuccessfulStrategy
   			-->
       		<bean class="org.apache.shiro.authc.pam.AtLeastOneSuccessfulStrategy"></bean>
       	</property>
   ```

   



# 授权

由于授权时需要使用到SecurityManger 里的 Realm，但是Realm配置到了Authenticator认证器中

因此改变一下配置文件

```xml
<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
        <property name="authenticator" ref="authenticator"/>
        <property name="realms">
            <list>
                <ref bean="jdbcRealm"/>
                <ref bean="secondRealm"/>
            </list>
        </property>
    </bean>
```

> 同时，删除认证器中的realms的配置
>
> 因为SecurityManager配置完realms之后，会将realms设置到authenticator（认证器）中



