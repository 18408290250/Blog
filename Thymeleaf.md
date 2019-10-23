

# Spring Boot系列之渲染引擎Thymeleaf

> 文档　   

- Thymeleaf官网：
https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#using-texts
https://www.thymeleaf.org/
- springBoot文档
https://docs.spring.io/spring-boot/docs/current/reference/html/
https://docs.spring.io/spring-boot/docs/current/reference/html/howto-hotswapping.html#howto-reload-thymeleaf-content
- WebFlux文档-View Technologies
https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-view-thymeleaf
- 

## 1.什么是Thymeleaf

Thymeleaf是一款用于渲染XML、XHTML、HTML5内容的模板引擎。类似Velocity，FreeMaker模板引擎，它也可以轻易的与Spring MVC等Web框架进行集成作为Web应用的模板引擎。

Thymeleaf也是Spring Boot首要支持的模板引擎，并且在最新的Spring Boot版本中已经不再支持Velocity了。

服务器端模板引擎



## 2.Spring Boot如何应用

### 自动配置说明
Thymeleaf的自动配置类：
>org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration

Thymeleaf的自动配置参数类：
>org.springframework.boot.autoconfigure.thymeleaf.ThymeleafProperties



查看源码参数：

```
	private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
 	static {
        DEFAULT_ENCODING = StandardCharsets.UTF_8;
    }

	//默认的编码是：UTF-8
	//默认的类型是：text/html
	//默认的模板文件目录是：classpath:/templates/
	//默认的模板文件后缀是：.html
	//这些参数都可以通过在application配置文件中指定spring.thymeleaf.xx进行更改
```



1.引入依赖。
说明：引入Spring Boot的Thymeleaf启动器依赖,注意：引入该依赖后会自动引入web依赖，不需要有再单独引入web依赖。（不太对）

```
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter-thymeleaf</artifactId>
	</dependency>

```

2.在resources目录下创建templates目录
3.在templates目录下创建.html模板文件
4.使用模板
```
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">

  <head>
    <title>Good Thymes Virtual Grocery</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all" 
          href="../../css/gtvg.css" th:href="@{/css/gtvg.css}" />
  </head>

  <body>
  
    <p th:text="#{home.welcome}">Welcome to our grocery store!</p>
  
  </body>

</html>

```

- xmlns:HTML xmlns 属性。xmlns 属性可以在文档中定义一个或多个可供选择的命名空间。浏览器会将此命名空间用于该属性所在元素内的所有内容。命名空间来规范它的语法。因为XHTML是用XML的语法来规范的HTML语言。这个标准就位于 http://www.w3.org/1999/xhtml 网址
- 这是一段标准的HTML代码，这也就意味着通过浏览器直接打开它是可以正确解析它的结构并看到页面的样子。相比去其他的模板引擎在指定的位置通过${}等表达式进行渲染，Thymeleaf则是一种针对HTML/XML定制的模板语言，它通过标签中的th:text属性来填充该标签的一段内容。
- <p>标签中的内容会被表达式#{home.welcome}的值所替代，无论模板中它的内容是什么，之所以在模板中“多此一举“地填充它的内容，完全是为了它能够作为原型在浏览器中直接显示出来。

```
<html xmlns="http://www.w3.org/1999/xhtml">

```



## 标准表达式语法

1.变量
Thymeleaf模板引擎在进行模板渲染时，还会附带一个Context存放进行模板渲染的变量，在模板中定义的表达式本质上就是从Context中获取对应的变量的值。

```
<p th:text="${name}">name</p>
```

2.URL
URL在Web应用模板中占据着十分重要的地位，需要特别注意的是语法@{...}来处理的。支持绝对路径URL和相对路径。
```
<a th:href="@{http://www.thymeleaf.org}">Thymeleaf,click me!!</a>
```

3.字符串替换
```
<span th:text="'Welcome to our application, ' + ${user.name} + '!'">
或
<span th:text="|Welcome to our application, ${user.name}!|">
注意： |...|中只能包含变量表达式${...}，不能包含其他常量、条件表达式
```

4.运算符
在表达式中使用各类算术运算符，例如+, -, *, /, %
```
<p th:with="isEven=(${count} % 2 == 0)">hello world</p>
th:with  是什么意思？？
```
使用逻辑运算符，>, <, <=',>=，==,!=都可以使用，唯一需要注意的是使用<,>`时需要用它的HTML转义符
```
<p th:if="${count} &gt; 6">hello world</p>
<p th:text="'Execution mode is ' + ( (${name} == 'thymeleaf')? 'thymeleaf' : 'hello')">hello world</p>
```
5.循环
被循环渲染的元素（这里是<tr>）中加入th:each标签，其中th:each="s : ${students}"students，循环变量是s在循环体中可以通过表达式访问。
```
 <tr th:each="s:${students}">
        <td th:text="${s.id}"></td>
        <td th:text="${s.name}"></td>
    </tr>
```
6.条件求值
```
<td><input th:type="checkbox" th:name="vip"  th:checked="( (${s.vip} == 1)? true : false)" th:value="${s.vip}"></td>
```
7.Switch.默认属性default可以用*表示
```
<div th:switch="${s.vip}">
	<p th:case="1"><input type="button" value="查看会员信息" class="btn" /></p>
	<p th:case="0"><input type="button" value="加入会员" class="btn" /></p>
	<p th:case="*">error</p>
</div>
```
8.Utilities.Thymeleaf提供了一系列Utility对象（内置于Context中），可以通过#直接访问  ？？
- dates
- calendars
- numbers
- strings
- arrays
- lists
- sets
- maps
- ...

9.文字国际化表达式。 #{…}获取国际化内容  ？？
文字国际化表达式允许我们从一个外部文件获取区域文字信息(.properties) 
使用Key-Value方式，还可以提供一组参数(可选).


##在JavaScript 中访问 model
通过th:inline="javascript"添加到script标签，这样JavaScript代码即可访问model中的属性，再通过[[${}]]格式来获得实际的值
```
var name = [[${name}]];
```



## 引入公共片段

引入公共片段的th属性，包括三种方式

- th:insert 保留自己的主标签，保留th:fragment的主标签
- th:replace 不要自己的主标签，保留th:fragment的主标签
- th:include 保留自己的主标签，不要th:fragment的主标签。（官方3.0后不推荐）

```
<!--footer.html-->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<footer th:fragment="footer1">
    <p>All Rights Reserved</p>
</footer>

<footer th:fragment="copy">
    &copy; 2011 The Good Thymes Virtual Grocery
</footer>
</html>

<!--hello.html-->
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>

```

## 内置对象

https://blog.csdn.net/pbrlovejava/article/details/82709606
https://www.cnblogs.com/jiangbei/p/8462294.html






