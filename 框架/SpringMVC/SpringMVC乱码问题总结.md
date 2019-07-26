# 乱码问题总结

### POST请求乱码

解决：在web.xml中添加过滤器

解释：CharacterEncodingFilter有两个属性

- encoding

- forceEncoding

  > 源码注释：
  >
  > Default is "false", i.e. do not modify the encoding if
  >
  > javax.servlet.http.HttpServletRequest.getCharacterEncoding()
  >
  > returns a non-null value. Switch this to "true" to enforce the specified
  >
  > encoding in any case, applying it as default response encoding as well.
  >
  > 中文解释：默认值为false，表示当request.getCharacterEncoding()返回非空时，不修改request的字符集
  >
  > 当设置为true时，强制执行
  >
  > request.setCharacterEncoding()和
  >
  > response.setCharacterEncoding(); 	

```xml
<!-- 解决post乱码问题 -->
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
	</init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



### GET请求乱码

*注意：get请求方式在tomcat7之前处理tomcat服务器都是用ISO-8859-1处理数据，之后的版本默认UTF-8*

**方法一：**

修改tomcat的server.xml配置文件，添加URIEncoding="utf-8"

```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" URIEncoding="utf-8"/>
```

**方法二：**

手动更改：

```java
String str = new String(request.getParameter("username").getBytes("ISO-8859-1"),"UTF-8");
```



### 返回响应乱码

#### 情况一：使用@ResponseBody直接返回对象

```java
@RequestMapping("testResponseBodyEncoding")
@ResponseBody
public Item testResponseBodyEncoding() {
	Item item = new Item();
	item.setId(333);
	item.setCreatetime(new Date());
	item.setDetail("使用ResponseBody返回json数据会乱码吗");
	item.setName("陈松彬");
	item.setPrice(1000d);
	return item; // 直接返回对象，不需要我们手动转为json格式数据
}
```

上述案例测试不会产生乱码，这是由于使用的转换器默认编码是UTF-8

![AbstractJackson2HttpMessageConverter默认编码](F:/%E4%B8%93%E6%B3%A8/SpringMVC/AbstractJackson2HttpMessageConverter%E9%BB%98%E8%AE%A4%E7%BC%96%E7%A0%81.JPG)



#### 情况二：使用@ResponseBody返回字符串

```java
@RequestMapping("testResponseBodyEncoding")
@ResponseBody
public String testResponseBodyEncoding() {
	Item item = new Item();
	item.setId(333);
	item.setCreatetime(new Date());
	item.setDetail("使用ResponseBody返回json数据会乱码吗");
	item.setName("陈松彬");
	item.setPrice(1000d);
	return item.toString();// 直接返回字符串
}
```

这种情况会产生乱码，这是由于使用的转换器默认编码是ISO-8859-1

![StringHttpMessageConverter默认编码](F:/%E4%B8%93%E6%B3%A8/SpringMVC/StringHttpMessageConverter%E9%BB%98%E8%AE%A4%E7%BC%96%E7%A0%81.JPG)

**解决办法：**

这里提供一个简单的解决办法：

在注解种设置produces的值

```java
@RequestMapping(value="testGet", produces = "application/json; charset=utf-8")
```

其他解决办法<https://www.cnblogs.com/striver-zhu/p/7158623.html>



#### 情况三：直接使用HttpServletResponse返回数据

这种情况需要手动设置

response.setContentType("text/html;charset=UTF-8");
response.setCharacterEncoding("UTF-8");

```java
@RequestMapping("getArray")
public void getArray(String[] names, HttpServletResponse response) throws IOException{
    response.setContentType("text/html;charset=UTF-8");
    response.setCharacterEncoding("UTF-8");
    PrintWriter writer = response.getWriter();
    for(int i=0;i<names.length;++i) {
        writer.println("第" + i + "个参数 = " + names[i]);
    }
    writer.flush();
}
```



