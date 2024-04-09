# JavaWeb

## Http

HTTP的头域包括通用头、请求头、响应头和实体头四个部分。每个头域由一个域名，冒号（:）和域值三部分组成（说白了就是键值对）。

- 通用头：是客户端和服务器都可以使用的头部，可以在客户端、服务器和其他应用程序之间提供一些非常有用的通用功能，如Date头部。
- 请求头：是请求报文特有的，它们为服务器提供了一些额外信息，比如客户端希望接收什么类型的数据，如Accept头部。
- 响应头：便于客户端提供信息，比如，客服端在与哪种类型的服务器进行交互，如Server头部。
- 实体头：指的是用于应对实体主体部分的头部，比如，可以用实体头部来说明实体主体部分的数据类型，如Content-Type头部。

### HTTP通用头

通用头域包含请求和响应消息都支持的头域，通用头域包含缓存头部Cache-Control、Pragma及信息性头部Connection、Date、Transfer-Encoding、Update、Via。

```json
{
    Request URL: https://www.cnblogs.com/renxuw/p/12994080.html
	Request Method:GET
	Status Code:200 OK
	Remote Address:121.40.43.188:443
	Referrer Policy:strict-origin-when-cross-origin
}
```

| **字段名**            | **含义**                                                     |
| --------------------- | ------------------------------------------------------------ |
| **Cache-Control**     | 告诉所有的缓存机制是否可用缓存及哪种类型。例：Cache-Control：no-cache |
| **Pragma**            | Pragma头域用来包含实现特定的指令，最常用的是Pragma:no-cache。在HTTP/1.1协议中，它的含义和Cache- Control:no-cache相同。 |
| **Connection**        | Connection表示是否需要持久连接。如果Servlet看到这里的值为“Keep-Alive”，或者看到请求使用的是HTTP 1.1（HTTP 1.1默认进行持久连接），它就可以利用持久连接的优点，当页面包含多个元素时（例如Applet，图片），显著地减少下载所需要的时间。 |
| **Date**              | Date头域表示消息发送的时间，服务器响应中要包含这个头部，因为缓存在评估响应的新鲜度时要用到，其时间的描述格式由RFC822定义。 |
| **Transfer-Encoding** | WEB 服务器表明自己对本响应消息体（不是消息体里面的对象）作了怎样的编码，比如是否分块（chunked），例如：Transfer-Encoding: chunked |
| **Upgrade**           | 它可以指定另一种可能完全不同的协议，如HTTP/1.1客户端可以向服务器发送一条HTTP/1.0请求，其中包含值为“HTTP/1.1”的Update头部，这样客户端就可以测试一下服务器是否也使用HTTP/1.1了。 |
| **Via**               | 列出从客户端到 OCS 或者相反方向的响应经过了哪些代理服务器，他们用什么协议（和版本）发送的请求。 |

### **请求头**

请求头用于说明是谁或什么在发送请求、请求源于何处，或者客户端的喜好及能力。服务器可以根据请求头部给出的客户端信息，试着为客户端提供更好的响应。

| **字段名**            | **含义**                                                     |
| --------------------- | ------------------------------------------------------------ |
| **Accept**            | 指定客户端能够接受的内容类型。例：Accept: text/plain, text/html |
| **Accept-Charset**    | 浏览器可以接受的字符编码集。                                 |
| **Accept-Encoding**   | 指定浏览器可以支持web服务器返回内容压缩编码类型              |
| **Accept-Language**   | 浏览器可以接受的语言                                         |
| **Authorization**     | 当客户端接收到来自WEB服务器的 WWW-Authenticate 响应时，用该头部来回应自己的身份验证信息给WEB服务器。 |
| **cookie**            | HTTP请求发送时，会把保存在该请求域名下所有的cookie值一起发送给web服务器。 |
| **If-Modified-Since** | 如果请求的部分时间修改则请求成功，未被修改返回[304](https://so.csdn.net/so/search?q=304&spm=1001.2101.3001.7020)代码 |
| **If-None-Match**     | 如果内容未改变返回304代码，参数为服务器先前发送的Etag，与服务器回应的Etag比较判断是否改变 |
| **Range**             | 浏览器（比如 Flashget 多线程下载时）告诉 WEB 服务器自己想取对象的哪部分。 |
| **User-Agent**        | User-Agent的内容包含发出的用户信息                           |
| **Content-Type**      | 请求的与实体对应MIME信息。例：Content-type：application/x-www-form-urlencoded |
| **Host**              | 指定请求的服务器和端口。例：Host：www.baidu.com              |
| **Referer**           | 先前网页的地址，告诉服务器来源。例：Referer：http://www.baidu.com/1.html |
| **User-Agent**        | 浏览器表明自己的身份（是哪种浏览器）。                       |

### **响应头**

响应头向客户端提供一些额外信息，比如谁在发送响应、响应者的功能，甚至与响应相关的一些特殊指令。这些头部有助于客户端处理响应，并在将来发起更好的请求。

| 字段名            | 含义                                                         |
| ----------------- | ------------------------------------------------------------ |
| **Age**           | 当代理服务器用自己缓存的实体去响应请求时，用该头部表明该实体从产生到现在经过多长时间了。 |
| **Server**        | WEB 服务器表明自己是什么软件及版本等信息。例如：Server：Apache/2.0.61 (Unix) |
| **Accept-Ranges** | WEB服务器表明自己是否接受获取其某个实体的一部分（比如文件的一部分）的请求。bytes：表示接受，none：表示不接受。 |
| **Vary**          | WEB服务器用该头部的内容告诉 Cache 服务器，在什么条件下才能用本响应所返回的对象响应后续的请求。假如源WEB服务器在接到第一个请求消息时，其响应消息的头部为：Content-Encoding: gzip; Vary: Content-Encoding，那么Cache服务器会分析后续请求消息的头部，检查其Accept-Encoding，是否跟先前响应的Vary头部值一致，即是否使用相同的内容编码方法，这样就可以防止Cache服务器用自己Cache 里面压缩后的实体响应给不具备解压能力的浏览器。例如：Vary：Accept-Encoding。 |

### 实体头

实体头部提供了有关实体及其内容的大量信息，从有关对象类型的信息，到能够对资源使用的各种有效的请求方法。总之，实体头部可以告知接收者它在对什么进行处理。请求消息和响应消息都可以包含实体信息，实体信息一般由**实体头域**和**实体**组成。

实体头域包含关于实体的原信息

实体头包括

- 信息性头部Allow、Location
- 内容头部Content-Base、Content-Encoding、Content-Language、Content-Length、Content-Location、Content-MD5、Content-Range、Content-Type
- 缓存头部Etag、Expires、Last-Modified、extension-header。

| **字段名**           | **含义**                                                     |
| -------------------- | ------------------------------------------------------------ |
| **Allow**            | 服务器支持哪些请求方法（如GET、POST等）。                    |
| **Location**         | 表示客户应当到哪里去提取文档，用于将接收端定位到资源的位置（URL）上。Location通常不是直接设置的，而是通过HttpServletResponse的sendRedirect方法，该方法同时设置状态代码为302。 |
| **Content-Base**     | 解析主体中的相对URL时使用的基础URL。                         |
| **Content-Encoding** | web服务支持的返回内容压缩编码类型                            |
| **Content-Language** | 响应体语言。例：Content-Language：en,ch                      |
| **Content-Length**   | 响应长度                                                     |
| **Content-type**     | WEB 服务器告诉浏览器自己响应的对象的类型。例如：Content-Type：application/xml |
| **Set-Cookie**       | 设置HTTP cookie。例Set-Cookie:UserID=JonhnDoe;Max-Age=3600;Version=1 |
| **ETag**             | 请求变量的实体标签的当前值。例：ETag：“737060cd8c284d8af7ad3082f209582d” |
| **Expires**          | WEB服务器表明该实体将在什么时候过期，对于过期了的对象，只有在跟WEB服务器验证了其有效性后，才能用来响应客户请求。是 HTTP/1.0 的头部。例如：Expires：Sat, 23 May 2009 10:02:12 GMT |

## **Tomcat**

### **安装与配置**

> **https://www.cnblogs.com/winton-nfs/p/13831076.html**

**注意一下jdk版本需要对应tamcat版本，否则会不成功**

### **目录说明**

**bin目录下**

**![image-20231102160939947](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021609501.png)**

**![image-20231102161256050](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021612147.png)**

**![image-20231102161346358](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021613400.png)**

## **Maven**

**为什么要使用Maven？它能帮助我们解决什么问题？**

- **添加第三方 jar 包:**

  **在今天的 JavaEE 开发领域，有大量的第三方框架和工具可以供我们使用。要使用这些 jar 包最简单 的方法就是复制粘贴到 WEB-INF/lib 目录下。但是这会导致每次创建一个新的工程就需要将 jar 包重复复 制到 lib 目录下，从而造成工作区中存在大量重复的文件，让我们的工程显得很臃肿。 而使用 Maven 后每个 jar 包本身只在本地仓库中保存一份，需要 jar 包的工程只需要以坐标的方式 简单的引用一下就可以了。不仅极大的节约了存储空间，让项目更轻巧，更避免了重复文件太多而造成 的混乱。**

- **jar 包之间的依赖关系**

  **jar 包往往不是孤立存在的，很多 jar 包都需要在其他 jar 包的支持下才能够正常工作，我们称之为 jar 包之间的依赖关系。引入 Maven 后，Maven 就可以替我们自动的将当前 jar 包所依赖的其他所有 jar 包全部导入进来， 无需人工参与，节约了我们大量的时间和精力。用实际例子来说明就是：通过 Maven 导入 commons-fileupload-1.3.jar 后，commons-io-2.0.1.jar 会被自动导入，程序员不必了解这个依赖关系。**

### **配置**

**新增环境变量名：MAVEN_HOME 和 M2_HOME；**

```
MAVEN_HOME: 安装目录bin目录的上一级目录
M2_HOME:安装目录bin目录
```

**在系统变量Path中新增**

```
 Path：%MAVEN_HOME%\bin            
```

**更改镜像源，加速下载**

**在maven安装目录conf目录下，修改settings.xml,在mirrors中注释原有镜像新增阿里云镜像**

```xml
<mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
```

**修改本地仓库，同样是在maven安装目录conf目录下，修改settings.xml，默认安装在**

```xml
Default: ${user.home}/.m2/repository
```

**也就当前用户的.m2目录下，本地仓库的作用是可以直接引用下载到本地的jar包，无需远程重新下载。**

### **idea中使用**

**高版本无法在创建项目时更改maven配置，会使用默认的maven，注意**

**![image-20231104163049800](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041630204.png)**

**一个干净的maven项目结构**

**![image-20231104163417506](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041634564.png)**

**maven项目之webapp项目结构，只有web应用才会有**

**![image-20231104163449496](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041640506.png)**

#### **idea配置本地Tomcat**

##### **步一：**

**![image-20231104163907453](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041639645.png)**

##### **步二：**

**![image-20231104163950713](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041639912.png)**

##### **步三：**

**![image-20231104164042117](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041640406.png)**

##### **步四：**

**![image-20231104164149364](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041641114.png)**

##### **步五：添加artifacts作为服务启动项**

**![image-20231104165104010](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041651805.png)**

**![image-20231104164802228](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041648442.png)**

##### **maven项目**

**![image-20231104165332984](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311041653813.png)**

##### **pom.xml**

```xml
<!--maven版本和头文件-->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <!--配置的GAV-->
  <groupId>com.balance</groupId>
  <artifactId>learnMaven</artifactId>
  <version>1.0-SNAPSHOT</version>

  <!--项目打包方式-->
  <packaging>war</packaging>

  <name>learnMaven Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <!--项目依赖-->
  <dependencies>
  <!--具体依赖的jar包-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <!--build项目构建所用 这里配置，来防止我们资源导出失败的问题-->
  <build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
    </resources>
  </build>
</project>
```

## **Servlet**

**Servlet就是Sun公司开发动态web的一门技术**

**Sun在这些API中提供一个接口叫做：Servlet，如果你想开发一个Servlet程序，只需要两个步骤**

- **编写一个类，实现Servlet接口**
- **把开发好的java类部署到web服务器中**

**把实现了Servlet接口的java程序叫做：Servlet**

### **第一个servlet程序**

**创建一个空maven项目，若没有空项目maven选项，创建一个webapp原型项目，删除不需要的文件即可。**

**在该项目pom.xml中添加servlet依赖**

```xml
<dependencies>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
    </dependency>
</dependencies>
```

**之后根项目右键创建module，命名为servlet-01，webapp原型项目**

**上面的操作创建了父子项目，父项目的依赖子项目可以直接用**

**之后在src/main/java包下，新建包，servlet，在其中创建HelloServlet类，完成后目录如下**

**![image-20231106161327792](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311061613364.png)**

**HelloServlet类继承默认实现的HttpServlet类，重写doGet方法**

```java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        PrintWriter writer = resp.getWriter();
        writer.print("this is servlet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

**在WEB-INF目录下修改web.xml让其和tomcat中xml一致**

**再在web.xml的web-app标签中编写servlet的映射如下**

```xml
<servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.balance.servlet.HelloServlet</servlet-class>
</servlet>
<!--servlet的请求路径-->
<servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

**为什么需要编写这个映射：**

- **我们写的是java程序，但是要通过浏览器访问，浏览器需要连接web服务器，所以我们需要再web服务中即web.xml中注册我们所写的Servlet,并且要给他一个浏览器可以访问的路径**

**最后配置Tomcat，运行**

```sql
http://localhost:8080/servlet_01_war/hello

# /servlet_01_war 配置tomcat指定的访问路径
# /hello 我们定义的Servlet请求路径
```

### **mapping相关问题**

**可以使用通配符来响应不在定义之内的页面**

```xml
<servlet-mapping>
    <servlet-name>error</servlet-name>
    <url-pattern>/*</url-pattern>
</servlet-mapping>
```

**设定了固有的路径优先级最高，若找不到才会返回通配符响应的页面。**

**还可以使用如下形式**

```xml
<servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>*.xxx</url-pattern>
</servlet-mapping>
```

**以上述方式进行映射，注意前面不能加具体路径，如/hello/*.xxx，会报错。**

### **ServletContext**

**web容器在启动的时候，会为每个web程序都创建一个对应的ServleContext对象，它代表了当前的web应用**

#### **共享数据**

**在一个web程序之中会有多个servlet，我们在一个servlet中向ServleContext存放数据后，在另一个servlet中也可以访问。**

**在第一个servlet中存**

```java
public class servlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // this.getInitParameter(); 初始化参数
        // this.getServletConfig(); servlet配置
        // this.getServletContext(); servlet上下文
        ServletContext servletContext = this.getServletContext();
        String userName="balance";
        servletContext.setAttribute("userName",userName);
    }
}
```

**在第二个servlet取**

```java
public class GetServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        String userName = (String) servletContext.getAttribute("userName");
        resp.setContentType("text/html");
        resp.setCharacterEncoding("UTF-8");
        resp.getWriter().print("userName="+userName);
    }
}
```

**在web.xml中配置路径**

```xml
<servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.balance.servlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
<servlet>
    <servlet-name>getS</servlet-name>
    <servlet-class>com.balance.GetServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>getS</servlet-name>
    <url-pattern>/getS</url-pattern>
</servlet-mapping>
```

**注意需要先访问第一个再访问第二个，因为存了才能取。**

#### **获取初始化参数**

**web.xml 可以有一些初始化参数，配置如下**

```xml
<!--配置web应用初始化参数-->
<context-param>
    <param-name>url</param-name>
    <param-value>jdbc:mysql://localhost:3306</param-value>
</context-param>

<!--配置映射-->
<servlet>
    <servlet-name>getParams</servlet-name>
    <servlet-class>com.balance.GetParams</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>getParams</servlet-name>
    <url-pattern>/getParams</url-pattern>
</servlet-mapping>
```

**可以通过ServletContext获取到**

```java
public class GetParams extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        String url = servletContext.getInitParameter("url");
        resp.getWriter().print(url);
    }
}
```

#### **请求转发**

**servletContext可以帮助我们完成请求转发(307)功能。注意，这不是重定向(302)，不会改变路径**

```java
public class ServletDispatcher extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        RequestDispatcher requestDispatcher = servletContext.getRequestDispatcher("/getParams");//转发的具体路径
        requestDispatcher.forward(req,resp);//调用forward实现具体转发
    }
}
```

```xml
<servlet>
    <servlet-name>dispatch</servlet-name>
    <servlet-class>com.balance.ServletDispatcher</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>dispatch</servlet-name>
    <url-pattern>/dispatch</url-pattern>
</servlet-mapping>
```

**![image-20231109152958466](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311091530299.png)**

#### **读取资源文件**

**当我们有两份资源文件，我们如何读取呢？**

**![image-20231109153240804](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311091532455.png)**

**首先，当项目运行时，这两份文件都会被打包到target文件目录下，classes中，我们俗称为类路径**

**注意在com.balance包下可能不会正常打包，可以在pom.xml加入**

```xml
<!--build项目构建所用 这里配置，来防止我们资源导出失败的问题-->
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

**![image-20231109153706813](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311091537042.png)**

**使用servletContext.getResourceAsStream将资源转换为流，从而Properties对象可以加载**

```java
public class getResources extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        InputStream resourceAsStream = servletContext.getResourceAsStream("/WEB-INF/classes/db.properties");//注意这是相对路径 / :当前项目 /WEB-INF:当前项目下的WEB-INF目录
        Properties properties = new Properties();
        properties.load(resourceAsStream);
        String userName = (String) properties.get("username");
        String password = (String) properties.get("password");

        resp.getWriter().print(userName+password);

    }
}
```

```xml
<servlet>
    <servlet-name>getRes</servlet-name>
    <servlet-class>com.balance.getResources</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>getRes</servlet-name>
    <url-pattern>/getRes</url-pattern>
</servlet-mapping>
```

**当我们使用上述的代码完成了任务之后，可以思考可不可以直接读取而不用servletContext呢**

```java
properties.load(new FileReader("/WEB-INF/classes/db.properties"));
```

**不幸的是，这是不可以的，因为FileReader使用的是文件系统路径，而servletContext.getResourceAsStream使用的是web路径，网站会报找不到资源文件的错误。**

**如果真的想用文件路径，也是有方法的，如下**

```java
String path =this.getServletContext().getRealPath("/WEB-INF/classes/db.properties");
FileInputStream in=new FileInputStream(path);
Properties props=new Properties();
props.load(in);
```

**getRealPath将web路径转为实际文件系统路径。**

![image-20231109164803246](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311091648255.png)

### **HttpServletResponse**

**web服务器接收到客户端的http请求，针对这个请求，分别创建一个代表请求的HttpServletRequest，和一个代表响应的一个HttpServletResponse**

- **如果要获取客户端请求过来的参数：HttpServletRequest**
- **如果要给客户端响应一些信息：HttpServletResponse**

#### **下载文件**

```java
public class FileDownLoader extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.获取下载路径
        String realPath = this.getServletContext().getRealPath("/WEB-INF/classes/img.png");
        System.out.println(realPath);
        //2.获取下载文件名 通过lastIndexOf截取最后的文件名
        String fileName = realPath.substring(realPath.lastIndexOf("\\") + 1);
        //3.设置响应头信息 注意设置编码，以免出现中文名称乱码等问题
        resp.setHeader("Content-Disposition" , "attachment;filename="+ URLEncoder.encode(fileName,"UTF-8"));
        //4.获取下载文件的输入流
        FileInputStream in = new FileInputStream(realPath);
        //5.创建缓冲区
        int len=0;
        byte[] bytes = new byte[1024];
        //6.获取下载文件的输出流
        ServletOutputStream outputStream = resp.getOutputStream();
        while((len=in.read(bytes))!=-1){
            outputStream.write(bytes,0,len);
        }
        in.close();
        outputStream.close();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

```xml
<servlet>
    <servlet-name>fileDown</servlet-name>
    <servlet-class>FileDownLoader</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>fileDown</servlet-name>
    <url-pattern>/fileDown</url-pattern>
</servlet-mapping>
```

#### **验证码**

```java
public class ImageServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //让浏览器 5s 刷新一次
        resp.setHeader("refresh","5");

        //在内存中创建一个图片
        BufferedImage img = new BufferedImage(80, 20, BufferedImage.TYPE_INT_RGB);
        //得到图片
        Graphics2D graphics = (Graphics2D) img.getGraphics();//笔
        //设置图片的背景颜色
        graphics.setColor(Color.white);//设置颜色
        graphics.fillRect(0,0,80,20);//填充
        //给图片写数据
        graphics.setColor(Color.BLUE);
        graphics.setFont(new Font(null,Font.BOLD,20));
        graphics.drawString(makeNum(),0,20);

        //告诉浏览器，这个请求用图片形式打开
        resp.setContentType("image/jpg");
        //让浏览器不缓存
        resp.setDateHeader("expires",-1);
        resp.setHeader("Cache-Control","no-cache");
        resp.setHeader("Pragma","np-cache");

        //把图片写给浏览器
        ImageIO.write(img,"jpg",resp.getOutputStream());
    }

    private String makeNum(){
        Random random = new Random();
        String num = random.nextInt(9999999)+"";
        StringBuffer stringBuffer = new StringBuffer();
        for (int i = 0; i <7-num.length() ; i++) {//保证是7位数，不足7位在前面填充0
            stringBuffer.append("0");
        }
        num= stringBuffer +num;
        return num;
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}

```

```xml
<servlet>
    <servlet-name>ImageServlet</servlet-name>
    <servlet-class>ImageServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>ImageServlet</servlet-name>
    <url-pattern>/img</url-pattern>
</servlet-mapping>
```

#### **实现重定向**

```java
public class RedirectHtml extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        /*  相当于
            resp.setHeader("Location","/servlet_03_down_war/img");
            resp.setStatus(302);
         */
        resp.sendRedirect("/servlet_03_down_war/img");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

```xml
<servlet>
        <servlet-name>RedirectHtml</servlet-name>
        <servlet-class>RedirectHtml</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>RedirectHtml</servlet-name>
        <url-pattern>/red</url-pattern>
    </servlet-mapping>
```

### **HttpServletRequest**

#### 获取前端传递的参数，请求转发

![image-20231115134108238](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311151341161.png)

```java
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //后台中文乱码问题 后面会学过滤器预处理请求，这部分会放在那里
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");

        String username=req.getParameter("username");
        String password=req.getParameter("password");
        String[] hobbies= req.getParameterValues("hobbies");//获得同名多个值用这个
        System.out.println("========================");
        System.out.println(username);
        System.out.println(password);
        System.out.println(Arrays.toString(hobbies));
        System.out.println("========================");

        //通过请求转发
        //这里的 / 代表当前的web应用
        req.getRequestDispatcher("/success.jsp").forward(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>登录</h1>
    <div style="text-align: center">
        <%--这里表单表示的意思：以post方式提交表单，提交到我们的login请求--%>
        <form action="${pageContext.request.contextPath}/loginServlet" method="post">
            用户名：<input type="text" name="username"> <br>
            密码：<input type="password" name="password"> <br>
            爱好：
            <input type="checkbox" name="hobbies" value="concert">演唱会
            <input type="checkbox" name="hobbies" value="sing">唱歌
            <input type="checkbox" name="hobbies" value="draw">画画
            <input type="checkbox" name="hobbies" value="dance">跳舞
            <br>
            <input type="submit">
        </form>
    </div>
</body>
</html>
```

```xml
<servlet>
        <servlet-name>loginServlet</servlet-name>
        <servlet-class>LoginServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>loginServlet</servlet-name>
        <url-pattern>/loginServlet</url-pattern>
    </servlet-mapping>
```

## Cookie & Session 重点

### Cookie

Cookie是浏览器在本地存储数据的一种方式，理论上是可以存储任何数据的，前提是数据的类型必须是String类型。

关于Cookie的三个问题：Cookie从哪来？Cookie到哪去？Cookie有什么用？

- **Cookie从哪来**：Cookie是从浏览器来的，服务器在响应时就会通过Set-Cookie字段将Cookie返回给浏览器，浏览器在下次访问服务器时，就会带上Cookie；
- **Cookie到哪去**：Cookie到服务器去，在下一次的浏览器访问服务器的时候，浏览器会带上服务器返回的Cookie去访问服务器；
- **Cookie有什么用**：Cookie是可以携带任何数据的，一般情况下我们是令Cookie来保存用户的登录信息，用来识别用户的身份，这样用户就不需要一直执行登录操作了。

#### **常用操作**

```java
Cookie[] cookies=req.getCookies();//获得cookie
cookie.getName();//获得cookie中的key
cookie.getValue();//获得cookie中的Value
Cookie cookie=new Cookie("lastLoginTime",System.currentTimeMillis()+"");//新建一个cookie
cookie.setMaxAge(24*60*60);//设置cookie的有效期
resp.addCookie(cookie);//响应给客户端一个cookie
```

#### **示例**

```java
public class SetCookies extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //解决中文乱码
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setHeader("Content-Type","text/html;charset=utf-8");

        PrintWriter out = resp.getWriter();
        //服务器从客户端获取cookie，有多个数组接收
        Cookie[] cookies = req.getCookies();

        //判断是否有cookie，第一次是没有的，需要设置
        if(cookies!=null){
            out.write("你上次访问的时间是：");
            for (int i = 0; i < cookies.length; i++) {
                Cookie cookie=cookies[i];
                //获取cookie的名字
                if(cookie.getName().equals("lastLoginTime")){
                    //获取cookie中的值
                    long lastLoginTime=Long.parseLong(cookie.getValue());
                    Date date=new Date(lastLoginTime);
                    out.write(date.toLocaleString());
                }
            }
        }else{
            out.print("这是第一次访问");
        }
        //添加最新的cookie
        Cookie lastLoginTime = new Cookie("lastLoginTime", System.currentTimeMillis() + "");
        resp.addCookie(lastLoginTime);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

```xml
<servlet>
    <servlet-name>setCookies</servlet-name>
    <servlet-class>SetCookies</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>setCookies</servlet-name>
    <url-pattern>/s1</url-pattern>
</servlet-mapping>
```

**注意上述代码中设置了 Content-Type ，因为仅仅设置 setCharacterEncoding ，只是响应回去的编码规则为utf-8，但是浏览会按默认规则进行解释，依旧会导致乱码**

#### 一些问题

**一个网站cookie是否存在上限！细节问题**

- 一个cookie只能保存一个信息
- 一个Web站点可以给浏览器发送多个cookie，IE5-IE6:20个，IE7:50个
- cookie大小限制为4kb

**删除cookie**：

- 不设置有效期，关闭浏览器，自动失效
- 设置有效期为0

**编解码问题：**

`URLDecoder` 和 `URLEncoder` 是 Java 中用于处理 URL 编码和解码的两个类。

1. **`URLEncoder`：**

   - 用于将字符串编码为符合 URL 规范的格式。主要用途是将字符串转换成适合放置在 URL 中的安全字符串。

   - 在 URL 中，一些字符（例如空格、问号、等号等）是不允许直接出现的，需要通过编码替换。`URLEncoder` 提供了 `encode(String s, String encoding)` 方法，将字符串进行 URL 编码。

     ```java
     String encodedString = URLEncoder.encode("This is a test string", "UTF-8");
     ```

   - 在上面的例子中，`encodedString` 将包含被替换成 `%` 后跟两位十六进制值的字符，以确保在 URL 中的正确传输。

2. **`URLDecoder`：**

   - 用于解码已经在 URL 中编码的字符串。当你从 URL 中获取参数时，这些参数可能已经被编码，需要使用 `URLDecoder` 将其还原。

   - 提供了 `decode(String s, String encoding)` 方法，将已编码的字符串进行解码。

     ```java
     String decodedString = URLDecoder.decode("This%20is%20a%20test%20string", "UTF-8");
     ```

   - 在上面的例子中，`decodedString` 将包含被还原成空格的原始字符串。

这些类是在处理涉及 URL 的网络编程或者 Web 开发时非常有用的工具，确保 URL 中的参数和数据正确传输，同时避免由于特殊字符而导致的问题。

### Session

上面的Cookie中，我们说到Cookie从服务器来，到服务器去，可以保存任何数据，一般是用来保存用户的登录信息。但是，服务器这边到底具体如何来区分用户的信息的呢？这里就需要用到Session(也称为会话)了，**Session就是服务器这边用来实现用户身份区分的一种机制**，一般和Cookie配合使用（也可以不和Cookie配合使用）。

什么是Session:

- 服务器会给每一个用户（浏览器）创建一个Session对象；
- 一个Session独占一个浏览器，只要浏览器没有关闭，这个Session就存在
- 用户登录后，整个网站它都可以访问！—>保存用户的信息；保存购物车的信息

#### 示例

**增加session属性，得到session id**

```java
public class SessionDemo01 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //解决乱码问题
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");

        //得到Session
        HttpSession session=req.getSession();
        //给Session中存东西
        session.setAttribute("name",new Person("balance",20));
        //获取Session的id
        String sessionId=session.getId();
        //判断Session是不是新创建
        if(session.isNew()){
            resp.getWriter().write("session创建成功，ID为："+sessionId);
        }else{
            resp.getWriter().write("session已经存在，ID为："+sessionId);
        }

        // Session创建的时候做了什么事情
        // Cookie cookie=new Cookie("name","达西");
        // resp.addCookie(cookie);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

```xml
<servlet-mapping>
    <servlet-name>sessionDemo01</servlet-name>
    <url-pattern>/session01</url-pattern>
</servlet-mapping>
```

**获得刚刚设置的session属性**

```java
public class SessionDemo02 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //解决乱码问题
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");

        //得到Session
        HttpSession session = req.getSession();
        //取session存储的属性值
        Person person = (Person) session.getAttribute("name");
        System.out.println(person.toString());
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

**移除Session中属性，设置Session为无效**

```java
public class SessionDemo03 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //解决乱码问题
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");

        //得到Session
        HttpSession session = req.getSession();
        //移除session中属性
        session.removeAttribute("name");
        //注销session
        session.invalidate();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}

```

**还可以在web.xml中设置session有效时间**

```xml
<!--设置Session默认的失效时间-->
<session-config>
    <!--15分钟之后Session自动失效，以分钟为单位-->
    <session-timeout>15</session-timeout>
</session-config>
```

### 两者区别

**相同点：**

1. **状态维护：** Cookies和Session都是用于在不同的HTTP请求之间维护状态信息的机制。
2. **存储信息：** 两者都可以用于存储用户的特定信息，以便在用户访问网站的不同页面时使用。

**不同点：**

1. **存储位置：**
   - **Cookie：** 存储在客户端（浏览器）中，以文本文件的形式存储在用户的计算机上。
   - **Session：** 存储在服务器上，通常存储在内存中，尽管有时也可能被持久化到数据库或文件系统中。
2. **安全性：**
   - **Cookie：** 相对较不安全，因为存储在客户端，可以被用户查看或修改。
   - **Session：** 相对较安全，因为存储在服务器端，客户端无法直接访问或修改。
3. **存储容量：**
   - **Cookie：** 有限制，通常每个域名下的Cookie总大小限制为4KB。
   - **Session：** 理论上可以存储更多的数据，取决于服务器的配置和可用资源。
4. **生命周期：**
   - **Cookie：** 可以设置过期时间，可以是会话级别（浏览器关闭即失效）或持久性的（在一定时间内有效）。
   - **Session：** 通常与用户的会话周期相同，**会话结束时失效**。
5. **使用场景：**
   - **Cookie：** 适用于需要在客户端保持状态信息的场景，例如记住用户登录状态、用户偏好设置等。
   - **Session：** 适用于需要在服务器端保持状态信息的场景，例如跟踪用户在网站上的活动、购物车信息等。

在实际应用中，通常会结合使用Cookies和Session，以发挥它们各自的优势。例如，可以使用Session来存储敏感信息，而使用Cookie来标识会话。

## Jsp

### **什么是JSP**

Java Sever Page : Java服务器端页面，也和Servlet一样，用于动态Web技术

最大的特点：

- 写JSP就像在写HTML

- 区别：

  - HTML只给用户提供静态的数据

  - JSP页面可以嵌入JAVA代码，为用户提供动态数据

### **jsp原理**

tomcat中有一个work目录

idea中使用tomcat的会在idea的tomcat中生产一个work目录

我电脑的地址：

```shell
C:\Users\122618942\AppData\Local\JetBrains\IntelliJIdea2021.3\tomcat\3f0acb11-4dc0-4295-b330-eafb4ba540e6\work\Catalina\localhost\Javaweb01_war_exploded\org\apache\jsp
```

发现页面转变成了Java程序,浏览器向服务器发送请求，不管访问什么资源，其实都是在访问Servlet

**JSP最终也会被转换成一个 JAVA类,JSP本质就是servlet**

```java
//初始化
public void _jspInit() {
}
//销毁
public void _jspDestroy() {
}
//JSPServlet
public void _jspService(HttpServletRequest request, HttpServletResponse response) {
}
```

1. 判断请求

2. 内置一些对象

   ```java
   final javax.servlet.jsp.PageContext pageContext; //页面上下文
   javax.servlet.http.HttpSession session = null; //session
   final javax.servlet.ServletContext application; //applicationContext
   final javax.servlet.ServletConfig config; //config
   javax.servlet.jsp.JspWriter out = null; //out
   final java.lang.Object page = this; //page:当前
   HttpServletRequest request //请求
   HttpServletResponse response //响应
   ```

3. 输出页面前增加的代码

   ```java
   response.setContentType("text/html; charset=utf-8");//设置响应的页面类型
   pageContext = _jspxFactory.getPageContext(this, request, response,
                                             null, true, 8192, true);
   _jspx_page_context = pageContext;
   application = pageContext.getServletContext();
   config = pageContext.getServletConfig();
   session = pageContext.getSession();
   out = pageContext.getOut();
   _jspx_out = out;
   ```

4. 以上的这些对象我们可以在JSP页面中直接使用

![image-20231120135346669](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311201355714.png)

**在JSP页面中：**

只要是JAVA代码就会原封不动的输出；

![image-20231120135243797](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311201352415.png)

如果是HTML代码，就会被转换为：

```
out.write("<html>\n");
```

这样的格式，输出到前端

## javaBean

**javaBean也就是java实体类**

JavaBean有特定的写法：

- 必须要有一个无参构造
- 属性必须私有化
- 必须有对应的get,set方法

一般用来和数据库的字段做映射 ORM

ORM：对象关系映射

- 表 —> 类
- 字段 —> 属性
- 行记录 —> 对象

## MVC

什么是MVC：Model view Controller

Servlet和 Jsp 都可以写JAVA代码，为了易于维护和使用，Servlet专注于处理请求以及控制视图跳转，Jsp专注于显示数据

### 早些年的架构

控制器：Controller Servlet

- 接收用户的请求

- 响应给客户端的内容
- 重定向或者转发

视图层：View Jsp

- 展示数据

- 提供可以供我们操作的请求

JavaBean (pojo) (entity)

JDBC

用户直接访问控制层，控制层就可以直接操作数据库
```
servlet-->CRUD(增删改查)-->数据库
弊端：程序十分臃肿，不利于维护
Servlet的代码中：处理请求，响应，视图跳转，处理JDBC，处理业务代码，处理逻辑代码
    
架构：没有什么是加一层解决不了的
程序员调用
    |
   JDBC
    |
Mysql Oracle SqlServer ...
```

### MVC三层架构

![image-20231120150458952](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311201505150.png)

Model

- 业务处理：业务逻辑（Service）

- 数据持久层：CRUD (Dao)

View

- 展示数据

- 提供链接发起Servlet请求（a,form,img…）

Controller (Servlet)

- 接收用户的请求 : ( req:请求参数，session信息… )
- 交给业务层处理对应的代码
- 控制视图的跳转

```
登录-->接收用户的登录请求-->处理用户的请求(获取用户登陆的参数，username,password...)-->交给业务层处理登录业务(判断用户名密码是否正确，事务)-->Dao层查询用户名和密码是否正确-->数据库
```

## Filter 过滤器 重点

Filter:过滤器，用来过滤网站的数据

- 处理中文乱码
- 登录验证…

![image-20231120152607374](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311201526465.png)

### Filter实现步骤

先新建项目，在pom.xml中加入如下依赖

```xml
<dependencies>
    <!--Servlet 依赖-->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
    </dependency>
    <!--jsp 依赖-->
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>javax.servlet.jsp-api</artifactId>
        <version>2.3.3</version>
    </dependency>
    <!--连接数据库-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.31</version>
    </dependency>
</dependencies>
```

### 导包

新建类，实现Filter接口，导包不要导错

​		![image-20231120153518798](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311201535221.png)

### 编写过滤器

实现Filter接口，重写对应的方法即可

```java
public class FilterDemo01 implements Filter {
    public FilterDemo01() {
        super();
    }

    //初始化：web服务器启动，就完成初始化了，随时等待过滤对象出现
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    /*
    chain：链
    1. 过滤中的所有代码，在过滤特定请求的时候都会执行
    2. 必须要让过滤器继续执行下去
    */
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletRequest.setCharacterEncoding("UTF-8");
        servletResponse.setCharacterEncoding("UTF-8");
        servletResponse.setContentType("text/html;charset=UTF-8");

        System.out.println("FilterDemo01 执行前");
        filterChain.doFilter(servletRequest,servletResponse);
        System.out.println("FilterDemo01 执行后");
    }

    //销毁：web服务器在关闭的时候，过滤会销毁
    @Override
    public void destroy() {
        System.out.println("FilterDemo01 销毁");
    }
}
```

在servlet中写入如下代码

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.getWriter().write("世界这么大");
}
```

### 在web.xml中配置Filter

```xml
<servlet>
    <servlet-name>showServlet</servlet-name>
    <servlet-class>com.balance.servlet.ShowServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>showServlet</servlet-name>
    <url-pattern>/servlet/show</url-pattern>
</servlet-mapping>
<servlet-mapping>
    <servlet-name>showServlet</servlet-name>
    <url-pattern>/show</url-pattern>
</servlet-mapping>

<filter>
    <filter-name>filterDemo01</filter-name>
    <filter-class>com.balance.FilterDemo01</filter-class>
</filter>
<filter-mapping>
    <filter-name>filterDemo01</filter-name>
    <!--只要是/servlet的任何请求，都会经过这个过滤器，不要图省事直接 /* 全部都走过滤器-->
    <url-pattern>/servlet/*</url-pattern>
</filter-mapping>
```

### 效果

访问/servlet/show时正常显示

![image-20231120155019250](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311201550460.png)

访问/show时未走过滤器，导致乱码

![image-20231120155100264](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311201551319.png)

### Filter实现权限拦截

#### 新建Login.jsp error.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="/learnFilter_war_exploded2/servlet/login" method="post">
    <input type="text" name="username">
    <input type="submit">
</form>
</body>
</html>
```

当提交表单时，提交到 /learnFilter_war_exploded2/servlet/login 路径

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    登录失败，用户名错误
    <a href="${pageContext.request.contextPath}/Login.jsp">返回首页</a>
</body>
</html>
```

error.jsp中提供一个返回首页的链接

#### web目录下新建sys目录

里面新建success.jsp 即登录成功页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
   登录成功
   <p><a href="/learnFilter_war_exploded2/servlet/logout">注销</a></p>
</body>
</html>
```

里面提供一个注销链接，请求发到 /learnFilter_war_exploded2/servlet/logout 中

#### LoginServlet 和 LogoutServlet

```java
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");//从参数里取
        if(username.equals("admin")){//登录成功
            req.getSession().setAttribute(ShareVar.USER_SESSION,req.getSession().getId());//在session中存储
            resp.sendRedirect("/learnFilter_war_exploded2/sys/success.jsp");
        }else{//登录失败
            resp.sendRedirect("/learnFilter_war_exploded2/error.jsp");
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

LoginServlet在登录时判断是否为admin，若是，向session中存储属性与值，并跳转到登录页，若不是，跳转到error页

```java
public class LogoutServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object attribute = req.getSession().getAttribute(ShareVar.USER_SESSION);
        if(attribute!=null){
            req.getSession().removeAttribute(ShareVar.USER_SESSION);
            resp.sendRedirect("/learnFilter_war_exploded2/Login.jsp");
        }else{
            resp.sendRedirect("/learnFilter_war_exploded2/Login.jsp");
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

LogoutServlet在注销时判断 session中在登录时所存属性和值是否还存在，若存在，移除并跳转回首页，若不存在，直接跳转回首页（防止不存在，页面卡在那里）

#### 新建Filter实现拦截请求

在做完上述操作之后，会有一个问题，我们可以通过在url栏直接输出地址从而访问到登录成功页面。Filter用于解决这个问题

```java
public class SysFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        //先将 ServletRequest 向下转型为 HttpServletRequest 从而获取到Session
        HttpServletRequest req = (HttpServletRequest)servletRequest;
        HttpServletResponse resp=(HttpServletResponse)servletResponse;

        Object attribute = req.getSession().getAttribute(ShareVar.USER_SESSION);
        if(attribute==null){
            resp.sendRedirect("/learnFilter_war_exploded2/error.jsp");
        }

        filterChain.doFilter(servletRequest,servletResponse);
    }
    @Override
    public void destroy() {

    }
}
```

#### xml 中注册

```xml
<servlet>
    <servlet-name>loginServlet</servlet-name>
    <servlet-class>com.balance.servlet.LoginServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>loginServlet</servlet-name>
    <url-pattern>/servlet/login</url-pattern>
</servlet-mapping>
<servlet>
    <servlet-name>logoutServlet</servlet-name>
    <servlet-class>com.balance.servlet.LogoutServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>logoutServlet</servlet-name>
    <url-pattern>/servlet/logout</url-pattern>
</servlet-mapping>

<filter>
    <filter-name>sysFilter</filter-name>
    <filter-class>com.balance.SysFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>sysFilter</filter-name>
    <url-pattern>/sys/*</url-pattern>
</filter-mapping>
```

## 监听器

实现一个监听器的接口；（有N种），以 HttpSessionListener 为例

### 编写一个监听器

```java
//统计网站在线人数：统计session
public class OnlineCountListener implements HttpSessionListener {
    //创建session监听：创建session就会触发
    @Override
    public void sessionCreated(HttpSessionEvent httpSessionEvent) {
        ServletContext servletContext = httpSessionEvent.getSession().getServletContext();
        Integer onlineCount = (Integer) servletContext.getAttribute("OnlineCount");
        if(onlineCount==null){
            onlineCount = new Integer(1);
        }else{
            int count=onlineCount.intValue();
            onlineCount=new Integer(count+1);
        }
        servletContext.setAttribute("OnlineCount",onlineCount);
    }

    //销毁session监听
    @Override
    public void sessionDestroyed(HttpSessionEvent httpSessionEvent) {
        ServletContext servletContext = httpSessionEvent.getSession().getServletContext();
        Integer onlineCount = (Integer) servletContext.getAttribute("OnlineCount");
        if(onlineCount==null){
            onlineCount = new Integer(0);
        }else{
            int count=onlineCount.intValue();
            onlineCount=new Integer(count-1);
        }
        servletContext.setAttribute("OnlineCount",onlineCount);
    }
}
```

### xml中注册监听器

```xml
<!-- 注册监听器   -->
<listener>
    <listener-class>com.balance.listener.OnlineCountListener</listener-class>
</listener>
```

## JDBC

**Java DataBase Connectivity**

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311211353803.png)

什么是JDBC：Java连接数据库

需要jar包的支持：

- jar.sql
- javax.sql
- mysql-connecter-java…连接驱动（必须要导入）

idea导入依赖,手动连接

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.31</version>
</dependency>
```

### JDBC固定步骤

1. **加载驱动**
2. **连接数据库，代表数据库**
3. **向数据库发送SQL的对象Statement:CRUD**
4. **编写SQL(根据业务，不同的sql)**
5. **执行SQL**
6. **关闭连接**

```java
public class TestJdbc {
    public static void main(String[] args) throws Exception {
        //配置信息,配置数据库路径时要配上数据库名称 jdbc:mysql://localhost:3306/jdbc01
        //?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC";
        String url="jdbc:mysql://localhost:3306/jdbc01?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC";
        String username="root";
        String password="";

        //1.加载驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2.连接数据库，代表数据库
        Connection connection= DriverManager.getConnection(url,username,password);
        //3.向数据库发送SQL的对象Statement:CRUD
        Statement statement = connection.createStatement();
        //4.编写SQL
        String sql="select * from users";

        //受影响的行数，增删改都使用executeUpdate
        //int i = statement.executeUpdate(sql);

        //5.执行SQL，返回一个ResultSet:结果集
        ResultSet rs= statement.executeQuery(sql);
        while (rs.next()){
            System.out.println("id"+" "+rs.getObject("id"));;
            System.out.println("name"+" "+rs.getObject("name"));;
            System.out.println("password"+" "+rs.getObject("password"));;
            System.out.println("email"+" "+rs.getObject("email"));;
            System.out.println("birthday"+" "+rs.getObject("birthday"));;
        }
        //6.关闭连接，释放资源（一定要做），先开后关
        rs.close();
        statement.close();
        connection.close();
    }
}
```

### 预编译SQL

```java
public class TestJdbc2 {
    public static void main(String[] args) throws Exception {
        //配置信息,配置数据库路径时要配上数据库名称 jdbc:mysql://localhost:3306/jdbc01
        //?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC";
        String url="jdbc:mysql://localhost:3306/jdbc01?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC";
        String username="root";
        String password="";

        //1.加载驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2.连接数据库，代表数据库
        Connection connection= DriverManager.getConnection(url,username,password);
        //3.编写SQL
        String sql="insert into users(id,name,password,email,birthday) values (?,?,?,?,?);";
        //4.预编译
        PreparedStatement preparedStatement= connection.prepareStatement(sql);

        preparedStatement.setInt(1,4);//给第一个占位符?的值赋值为4
        preparedStatement.setString(2,"张三");//给第二个占位符?的值赋值为赵六
        preparedStatement.setString(3,"164646");//给第三个占位符?的值赋值为654321
        preparedStatement.setString(4,"zhangsan@qq.com");//给第四个占位符?的值赋值为zhangsan@qq.com
        preparedStatement.setDate(5,new java.sql.Date(new java.util.Date().getTime()));//给第五个占位符?的值赋值为new java.sql.Date(new java.util.Date().getTime())
        //5.执行SQL
        int i= preparedStatement.executeUpdate();
        if(i>0){
            System.out.println("插入成功");
        }
        //6.关闭连接，释放资源（一定要做），先开后关
        preparedStatement.close();
        connection.close();
    }
}
```

### 事务

数据库事务执行的四个基本要素

ACID:atomicity、consistency、isolation、durability

原子性，持久性，隔离性，一致性

#### 事务回滚

```java
public class TestJdbc3 {
    @Test
    public void test() throws Exception{
        //配置信息,配置数据库路径时要配上数据库名称 jdbc:mysql://localhost:3306/jdbc01
        //?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC";
        String url="jdbc:mysql://localhost:3306/jdbc01?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC";
        String username="root";
        String password="";
        Connection connection=null;
        try{
        //1.加载驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2.连接数据库，代表数据库
        connection= DriverManager.getConnection(url,username,password);
        //3.通知数据库开启事务，false代表开启
        connection.setAutoCommit(false);

        String sql="update account set money = money-100 where NAME ='A';";
        connection.prepareStatement(sql).executeUpdate();
        //制造错误
        int i=1/0;
        String sql2="update account set money = money+100 where NAME ='B';";
        connection.prepareStatement(sql2).executeUpdate();
        connection.commit();//以上两条SQL都执行成功了，就提交事务
        System.out.println("success");
        }catch (Exception e){
            try {
                //如果出现异常，就通知数据库回滚事务
                connection.rollback();
            }catch (SQLException e1){
                e.printStackTrace();
            }
            e.printStackTrace();
        }finally {
            try{
                connection.close();
            }catch (SQLException e){
                e.printStackTrace();
            }
        }
    }
}
```

## 文件上传

### 准备工作

对于文件上传，浏览器在上传的过程中是将文件以流的形式提交到服务器的，一般选择采用apache的开源工具common-fileupload这个jar包。

common-fileupload依赖于common-io包，所以还需要下载这个包

此处不使用maven导包，采用手动导包的方式作为参考

首先，新建lib目录，将jar包复制进去

![image-20231122135503564](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311221355811.png)

注意此时，项目运行，并不会将这两个jar包导入，有两种方式解决

方式一：右键lib目录，选择Add as library

![image-20231122135625292](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311221356343.png)

方式二：File中选择项目结构，添加到library

![image-20231122135833049](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311221358837.png)

若有其他问题，在Problems中点击修复即可

### 注意事项

1. 为保证服务器安全，**上传文件应该放在外界无法直接访问的目录**下，比如放于WEB-INF目录下
2. 为防止文件覆盖的现象发生，要为上传文件产生一个唯一的文件名（**时间戳 uuid md5等保证唯一性**）
3. 要限制**上传文件的最大值**
4. 可以限制上传文件的类型，在收到上传文件名时，**判断后缀是否合法**

### 使用到的类简介

**ServletFileUpload**负责处理上传的文件数据，并将表单中每个输入项封装成一个**Fileltem**对象，在使用**ServletFileUpload**对象解析请求时需要**DiskFileltemFactory**对象。

所以，我们需要在进行解析工作前构造好**DiskFileltemFactory**对象，通过**ServletFileUpload**对象的构造方法或**setFileltemFactory**方法设置**ServletFileUpload**对象的**fileltemFactory**属性。

### 具体实现

项目结构图

![image-20231122152401148](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311221524321.png)

FileServlet

```java
public class FileServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //判断上传的表单是普通表单还是带文件的表单，是返回true,否返回false；
        if (!ServletFileUpload.isMultipartContent(req)) {
            return;//如果这是一个普通文件我们直接返回
        }//如果通过了这个if，说明我们的表单是带文件上传的


        //创建上传文件的保存目录，为了安全建议在WEB-INF目录下，用户无法访问
        String uploadPath = this.getServletContext().getRealPath("/WEB-INF/Upload");//获取上传文件的保存路径
        File uploadFileDir = new File(uploadPath);
        if (!uploadFileDir.exists()) {
            uploadFileDir.mkdir();//如果目录不存在就创建这样一个目录
        }


        //临时文件
        //临时路径，如果上传的文件超过预期的大小，我们将它存放到一个临时目录中，过几天自动删除，或者提醒用户转存为永久
        String tmpPath = this.getServletContext().getRealPath("/WEB-INF/tmp");
        File tmpFileDir = new File(tmpPath);
        if (!tmpFileDir.exists()) {
            tmpFileDir.mkdir();//如果目录不存在就创建这样临时目录
        }
        //处理上传的文件一般需要通过流来获取，我们可以通过request.getInputstream(),原生态文件上传流获取，十分麻烦
        //但是我们都建议使用Apache的文件上传组件来实现，common-fileupload,它需要依赖于common-io组件；

        try {
            //1、创建DiskFileItemFactory对象，处理文件上传路径或限制文件大小
            DiskFileItemFactory factory = gteDiskFileItemFactory(tmpFileDir);
            //2、获取ServletFileUpload
            ServletFileUpload upload = getServletFileUpload(factory);
            //3、处理上传文件

            String msg = uploadParseRequest(upload, req, uploadPath);
            //Servlet请求转发消息
            req.setAttribute("msg", msg);
            req.getRequestDispatcher("info.jsp").forward(req, resp);
        } catch (FileUploadException e) {
            e.printStackTrace();
        }
    }
    public static DiskFileItemFactory gteDiskFileItemFactory(File tmpFileDir) {
        //1、创建DiskFileItemFactory对象，处理文件上传路径或限制文件大小
        DiskFileItemFactory factory = new DiskFileItemFactory();

        //通过这个工厂设置一个缓冲区，当上传的文件大小大于缓冲区的时候，将它放到临时文件夹tmpFileDir中；
        factory.setSizeThreshold(1024 * 1024);//缓冲区大小为1M
        factory.setRepository(tmpFileDir);
        return factory;
    }

    public static ServletFileUpload getServletFileUpload(DiskFileItemFactory factory) {
        //2、获取ServletFileUpload
        ServletFileUpload upload = new ServletFileUpload(factory);
        //监听文件上传进度
        upload.setProgressListener(new ProgressListener() {
            @Override
            public void update(long pBytesRead, long lpContentLenght, int i) {
                DecimalFormat df=new DecimalFormat("0.00");
                //pBytesRead:已读取到的文件大小
                //pContentLenght：文件大小
                System.out.println("总大小：" + lpContentLenght + "已上传：" + df.format((float)pBytesRead/lpContentLenght));
            }
        });
        //处理乱码问题
        upload.setHeaderEncoding("UTF-8");
        //设置单个文件的最大值
        upload.setFileSizeMax(1024 * 1024 * 10);
        //设置总共能够上传文件的大小
        //1024 = 1kb * 1024 = 1M * 10 = 10M
        upload.setSizeMax(1024 * 1024 * 10);
        return upload;
    }

    public static String uploadParseRequest(ServletFileUpload upload, HttpServletRequest request, String uploadPath) throws IOException, FileUploadException {
        String msg = "";
        //3、处理上传文件
        //把前端的请求解析，封装成一个FileItem对象
        List<FileItem> fileItems = upload.parseRequest(request);
        for (FileItem fileItem : fileItems) {
            if (fileItem.isFormField()) { //判断是普通表单还是带文件的表单
                //getFieldName指的是前端表单控件的name
                String name = fileItem.getFieldName();
                String value = fileItem.getString("UTF-8");//处理乱码
                System.out.println(name + ":" + value);
            } else {//判断它是带文件的表单
                //======================处理文件=======================//
                //拿到文件的名字
                String uploadFileName = fileItem.getName();
                System.out.println("上传的文件名：" + uploadFileName);
                if (uploadFileName.trim().equals("")) continue;

                //获得上传的文件名，例如/img/girl/ooa.jpg,只需要ooa，其前面的后面的都不需要
                String fileName = uploadFileName.substring(uploadFileName.lastIndexOf("/") + 1);
                //获得文件的后缀名
                String fileExtName = uploadFileName.substring(uploadFileName.lastIndexOf(".") + 1);
                /*
                 * 如果后缀名 fileExtName 不是我们需要的
                 *就直接return，不处理，告诉用户类型不对
                 * */
                System.out.println("文件信息【文件名：" + fileName + "文件类型：" + fileExtName + "】");
                //可以使用UUID(唯一通用识别码)来保证文件名的统一
                String uuidFileName = UUID.randomUUID().toString();
                //=======================传输文件=========================//
                //获得文件上传的流
                InputStream inputStream = fileItem.getInputStream();
                //创建一个文件输出流
                FileOutputStream fos = new FileOutputStream(uploadPath + "/" + uuidFileName + "." + fileExtName);
                //创建一个缓冲区
                byte[] buffer = new byte[1024 * 1024];
                //判断是否读取完毕
                int len;
                //如果大于0，说明还存在数据
                while ((len = inputStream.read(buffer)) > 0) {
                    fos.write(buffer, 0, len);
                }
                //关闭流
                fos.close();
                inputStream.close();
                msg = "文件上传成功！";
                fileItem.delete();//上传成功，清除临时文件
            }
        }
        return msg;
    }
}
```

index.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
      通过表单上传文件
      get:上传文件大小有限制
      post:上传文件大小没有限制
      --%>
    <form action=" ${pageContext.request.contextPath}/upload.do" method="post" enctype="multipart/form-data">
        上传用户：<input type="text" name="username"><br>
        <p><input type="file" name="file1"></p>
        <p><input type="file" name="file2"></p>

        <p><input type="submit"> | <input type="reset"></p>
    </form>
</body>
</html>
```

info.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

结果====> ${msg}

</body>
</html>
```

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0"
         metadata-complete="true">
  <servlet>
    <servlet-name>fileServlet</servlet-name>
    <servlet-class>com.balance.servlet.FileServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>fileServlet</servlet-name>
    <url-pattern>/upload.do</url-pattern>
  </servlet-mapping>

</web-app>
```

上传后目录

![image-20231122152654956](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311221526691.png)

## 邮件传输

![在这里插入图片描述](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311231447232.png)

### 邮件收发

> 要在网络上实现邮件功能，必须要有专门的**邮件服务器**。

这些邮件服务器类似于现实生活中的邮局，

它主要负责接收用户投递过来的邮件，并把邮件投递到邮件接收者的电子邮箱中。
对应：接收和发送
接收：POP3协议
发送：SMTP协议
SMTP服务器地址：一般是smtp.xxx.com，每一个网站都有自己的服务器地址（固定的）

比如163邮箱是smtp.163.com，qq邮箱是smtp.qq.com。

比如我们要使用QQ邮箱，就需要开通QQ邮箱的服务

![image-20231123144527350](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311231445526.png)

（1）张三通过smtp协议连接到Smtp服务器，然后发送一封邮件给网易的邮件服务器

（2）网易分析发现需要去QQ的邮件服务器，通过smtp协议将邮件转投给QQ的Smtp服务器

（3）QQ将接收到的邮件存储在lisi@qq.com这个邮件账号的空间中

（4）李四通过Pop3协议连接到Pop3服务器收取邮件

（5）从lisi@qq.com这个邮件账号的空间中取出邮件

（6）Pop3服务器将取出来的邮件送到李四手中

### Java发送邮件

首先导入依赖的jar包，分别为：

- mail.jar
- activation.jar

这里依然采用maven导入依赖，手动导入参照文件上传的示例

主要有四个核心类，我们在编写程序时，记住这四个核心类，就很容易编写出Java邮件处理程序。

![在这里插入图片描述](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311231450508.png)

**Session：定义整个程序，所有的信息。比如：smtp.qq.com、邮箱账号、密码**

**Transport：发送邮件**

**Message：信息的内容。对应：收件人、主题、正文**

#### 普通邮件-文本

```java
public class MailSender {
    public static void main(String[] args) throws Exception{
        //Properties中 设置属性
        Properties prop=new Properties();
        prop.setProperty("mail.host","smtp.qq.com");///设置QQ邮件服务器
        prop.setProperty("mail.transport.protocol","smtp");///邮件发送协议
        prop.setProperty("mail.smtp.auth","true");//需要验证用户密码

        //QQ邮箱需要设置SSL加密，原因：大厂。其他邮箱不需要
        MailSSLSocketFactory sf = new MailSSLSocketFactory();
        sf.setTrustAllHosts(true);
        prop.put("mail.smtp.ssl.enable","true");//使用安全的连接为true
        prop.put("mail.smtp.ssl.socketFactory",sf);//socket工厂，使用自己的socket工厂

        //使用javaMail发送邮件的5个步骤
        //1.创建定义整个应用程序所需要的环境信息的session对象

        //QQ才有!其他邮箱就不用
        Session session= Session.getDefaultInstance(prop, new Authenticator() {//获取默认的实例
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                //发件人邮箱、授权码
                return new PasswordAuthentication("xxxxxxxx@qq.com","xxxxxxxx");//后面是授权码
            }
        });

        //开启session的debug模式，这样可以查看到程序发送Email的运行状态
        session.setDebug(true);

        //2.通过session得到transport对象
        Transport ts = session.getTransport();

        //3.使用邮箱的用户名和授权码连上邮件服务器
        ts.connect("smtp.qq.com","xxxxxxxxx@qq.com","xxxxxxxx");

        //4.创建邮件：写邮件
        //需要传递session
        MimeMessage message = new MimeMessage(session);

        //指明邮件的发件人
        message.setFrom(new InternetAddress("xxxxxxxx@qq.com"));

        //指明邮件的收件人，现在发件人和收件人是一样的，那就是自己给自己发
        message.setRecipient(Message.RecipientType.TO, new InternetAddress("xxxxxxxx3@qq.com"));

        //邮件的标题   只包含文本的简单邮件
        message.setSubject("发送的标题");

        //邮件的文本内容
        message.setContent("你好，这是一封测试邮件","text/html;charset=UTF-8");

        //5.发送邮件
        ts.sendMessage(message,message.getAllRecipients());

        //6.关闭连接，一切网络都需要关闭
        ts.close();

    }
}
```

#### 带图片和附件的邮件

- MimeBodyPart类 （内容的主体）

  - javax.mail.internet.MimeBodyPart类

  - 表示的是一个MIME消息，它和MimeMessage类一样都是从Part接口继承过来。

- MimeMultipart类 （内容的封装）

  - javax.mail.internet.MimeMultipart是抽象类

  - Multipart的实现子类，它用来组合多个MIME消息。

  - 一个MimeMultipart对象可以包含多个代表MIME消息的MimeBodyPart对象。

![image-20231123151027232](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311231510937.png)

在MimeBodyPart中有三个属性

- mixed：附件
- related：内嵌资源
- alternative：超文本正文

![在这里插入图片描述](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311231510363.png)

```java
public class MailDemo01 {
    public static void main(String[] args) throws Exception {

        //Properties中 设置属性
        Properties prop=new Properties();
        prop.setProperty("mail.host","smtp.qq.com");///设置QQ邮件服务器
        prop.setProperty("mail.transport.protocol","smtp");///邮件发送协议
        prop.setProperty("mail.smtp.auth","true");//需要验证用户密码

        //QQ邮箱需要设置SSL加密，原因：大厂。其他邮箱不需要
        MailSSLSocketFactory sf = new MailSSLSocketFactory();
        sf.setTrustAllHosts(true);
        prop.put("mail.smtp.ssl.enable","true");//使用安全的连接为true
        prop.put("mail.smtp.ssl.socketFactory",sf);//socket工厂，使用自己的socket工厂

        //使用javaMail发送邮件的5个步骤
        //1.创建定义整个应用程序所需要的环境信息的session对象

            //QQ才有!其他邮箱就不用
        Session session= Session.getDefaultInstance(prop, new Authenticator() {//获取默认的实例
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                //发件人邮箱、授权码
                return new PasswordAuthentication("1455272942@qq.com","evdqbmnahqxjhfbi");
            }
        });

            //开启session的debug模式，这样可以查看到程序发送Email的运行状态
        session.setDebug(true);

        //2.通过session得到transport对象
        Transport ts = session.getTransport();

        //3.使用邮箱的用户名和授权码连上邮件服务器
        ts.connect("smtp.qq.com","1455272942@qq.com","evdqbmnahqxjhfbi");

        //4.创建邮件：写邮件
            //需要传递session
        MimeMessage message = new MimeMessage(session);

            //指明邮件的发件人
        message.setFrom(new InternetAddress("1455272942@qq.com"));

            //指明邮件的收件人，现在发件人和收件人是一样的，那就是自己给自己发
        message.setRecipient(Message.RecipientType.TO, new InternetAddress("1455272942@qq.com"));

            //邮件的标题   只包含文本的简单邮件
        message.setSubject("发送的标题");

            //邮件的文本内容
        message.setContent("你好","text/html;charset=UTF-8");

        /*==================图片和附件的邮件=========================*/


        //=================================准备图片数据
        MimeBodyPart image = new MimeBodyPart();
        //图片需要经过数据化的处理  DataHandler：数据处理    FileDataSource：加载文件的资源
        DataHandler dh = new DataHandler(new FileDataSource("D:\\学习资料\\MarkDown学习\\狂神说java\\javaWeb\\功能扩展\\功能扩展.assets\\邮件发送.png"));
        //在part中放入这个处理的图片数据
        image.setDataHandler(dh);
        //给这个part设置一个ID名字
        image.setContentID("bz.jpg");

        //=================================准备正文数据
        MimeBodyPart text = new MimeBodyPart();
        text.setContent("这是一张正文<img src='cid:bz.jpg'>","text/html;charset=UTF-8");

        //=================================准备附件数据
        MimeBodyPart body1= new MimeBodyPart();
        body1.setDataHandler(new DataHandler(new FileDataSource("D:\\学习资料\\MarkDown学习\\狂神说java\\javaWeb\\功能扩展\\功能扩展.txt")));
        body1.setFileName("1.txt");

        //描述数据关系
        MimeMultipart mm = new MimeMultipart();
        mm.addBodyPart(body1);
        mm.addBodyPart(text);
        mm.addBodyPart(image);
        mm.setSubType("mixed");

        //设置到消息中，保存修改
        message.setContent(mm);
        message.saveChanges();

        /*===========================================*/

        //5.发送邮件
        ts.sendMessage(message,message.getAllRecipients());

        //6.关闭连接，一切网络都需要关闭
        ts.close();

    }
}
```

