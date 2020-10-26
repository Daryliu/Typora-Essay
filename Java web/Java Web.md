### 1、web服务器

服务器用于处理用户的请求，并返回给用户一些相应信息。

#### 1.1 IIS

微软的，ASP，用于windows种自带的

#### 1.2 Tomcat

（作者暂定端口为8086）

实际运行JSP和Servlet。（Java的跟加载机制实际是运行jdk中的rt.jar）

​	bin文件夹：启动、关闭的脚本文件

​	conf文件夹：配置

​	lib文件夹：依赖的jar包

​	logs文件夹：日志

​	temp文件夹：临时文件

​	webapps文件夹：存放网站的

​	work文件夹：工作文件夹

##### 1.2.1 配置

conf文件夹下server.xml文件

- http端口号默认80
- https默认端口号443
- mysql默认端口号3306

```xml
	<!--修改端口号为8086-->
<Connector port="8086" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

可以配置主机名称

- 默认主机名：localhost->127.0.0.1
- 默认网站应用存放的位置：webapps

```xml
	<!--修改主机名-->
<Host name="www.ldy.com"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
    则输入的是时候可以输入www.ldy.com:8086进入tomcat网站
```

**面试题：**

请谈一谈网站是如何进行访问的？

1. 输入一个域名；回车

2. 检查本机`C:\Windows\System32\drivers\etc`该目录下的域名的映射配置

   1. 有：直接返回对应的ip地址，这个地址中有我们需要访问的web程序，可以直接访问
   2. 没有：去DNS（全世界的域名）服务器上找，找到就返回，找不到就返回找不到

   <img src="C:\Users\dn3\AppData\Roaming\Typora\typora-user-images\image-20200926164638642.png" alt="image-20200926164638642" style="zoom: 80%;" />



### 2、Http

Http（超文本传输协议）：请求响应协议，通常运行在TCP上。

- 超文本：图片、音乐、视频、定位、地图...	
- 文本：html、字符串...

#### 2.1 Http请求

- 客户端---发请求request---服务器

例子：百度

```Java
Request URL: https://www.baidu.com/     请求地址
Request Method: GET			请求方法get/post
Status Code: 200 OK			状态码：200   3开头  5开头   4开头
Remote Address: 112.80.248.76:443		实际访问的远程地址
Referrer Policy: unsafe-url		协议
```

1. **请求行**
   - 请求行中的请求方式get
   - 请求方式：`get、post、head、delete、put、tract`
     - ​	get：请求携带的参数较少，大小有限制，会在浏览器的url地址栏显示数据内容。不安全；但是高效
     - ​    post：与get相反
2. **消息头**

“请求和响应”通用的部分：

```Java
Accept: text/html		告诉浏览器它所支持的数据类型
Accept-Encoding: gzip, deflate, br			告诉浏览器所支持的编码格式 GBK,UTF-8,GB2312  ISO8859-1
Accept-Language: zh-CN,zh;q=0.9,zh-TW;q=0.8,en-US;q=0.7,en;q=0.6			设置语言环境
Connection: keep-alive		告诉浏览器请求完成是断开还是保持连接
cache-Control:  缓存控制
Host:			主机
```



#### 2.2 Http响应

```java
Cache-Control: private			缓存控制
Connection: keep-alive			连接
Content-Encoding: gzip			编码
Content-Type: text/html;charset=utf-8		类型
```

1. **响应体**

```Java
Accept: text/html		告诉浏览器它所支持的数据类型
Accept-Encoding: gzip, deflate, br			告诉浏览器所支持的编码格式 GBK,UTF-8,GB2312  ISO8859-1
Accept-Language: zh-CN,zh;q=0.9,zh-TW;q=0.8,en-US;q=0.7,en;q=0.6			设置语言环境
Connection: keep-alive		告诉浏览器请求完成是断开还是保持连接
cache-Control:  缓存控制
Host:			主机
Refresh:  		告诉客户端多久定时刷新
Location:		让网页重新定位
```

1. **响应状态码**

   - 200：请求成功
   - 3**：请求重定向
     - 重定向：你重新到我给你的新地址去
   - 4**：资源不存在
   - 5**：服务器代码错误      
     - 502网关错误

   **面试题**

   ​	<font color = "yellowblue">当你的浏览器中地址栏输入地址并回车的一瞬间到页面能够展示回来经历了什么？</font>

   

### 3、Maven项目架构管理工具

**自动导入和配置jar包。**

核心思想：**约定大于配置**

- ​	有约束，不要去违反，必须要按照规范来

配置仓库地址：

```xml
在maven的conf中配置
<localRepository>D:\apache-maven-3.6.3\maven-repo</localRepository>
```

配置镜像：

```xml
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url> 
</mirror>
```



`pom.xml`文件

核心配置文件

```xml
<!--配置的GAV-->
  <groupId>com.ldy</groupId>
  <artifactId>MavenDemo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!--Java web打包方式-->
  <packaging>war</packaging>

  <name>MavenDemo Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <!--默认构建编码-->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--编译时输出的目标版本-->
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <!--项目依赖-->
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
<build>
        <!--配置以下防止我们自己写在java和resources中的xml资源导出失败的问题-->
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <excludes>
                    <exclude>**/*.properties</exclude>
                    <exclude>**/*.xml</exclude>
                </excludes>
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



### 4、Servlet

是动态web的技术；是sun公司提供的接口。----->Servlet是实现了servlet接口的Java程序。

<u>有两个默认的实现类HttpServlet和GenericServlet</u>，而HttpServlet继承于GenericServlet，所以**只需要继承HttpServlet**

#### 4.1 创建步骤

- #### 创建一个普通的maven项目 （src文件夹可删除，用module创建子项目）

- maven父子工程

  ​	父项目中会有

  ```xml
  <modules>
          <module>servlet-01</module>
      </modules>
  ```

  ​	子项目会有：

  ```xml
  <parent>
          <artifactId>MavenProject</artifactId>
          <groupId>com.daryl</groupId>
          <version>1.0-SNAPSHOT</version>
      </parent>
  ```

  ​	父项目中的Java子项目可以直接使用：

  ```Java
  son extends father
  ```

  

#### 4.2 `web.xml`

使得web服务器与servlet连接的方法

```xml
<!--  注册servlet-->
  <servlet>
    <servlet-name>helloServlet</servlet-name>
    <servlet-class>com.daryl.servlet.HelloServlet</servlet-class>//(指明该servlet在哪个包下)
  </servlet>
<!--  一个servlet一个对应的Mapping：映射-->
  <servlet-mapping>
    <servlet-name>helloServlet</servlet-name>
    <!--映射请求路径   输入helloServlet后会通过servlet-name找到注册的servlet，然后找到servlet-class的路径执行-->
    <url-pattern>/helloServlet</url-pattern>
  </servlet-mapping>
```



#### 4.3 Servlet原理

Servlet是由web服务器调用，web服务器收到浏览器请求后，会：

![image-20201006160715605](C:\Users\dn3\AppData\Roaming\Typora\typora-user-images\image-20201006160715605.png)



#### 4.4 Mapping问题

请求映射的路径（相当于前端的路由）；

- 一个Servlet可以指定多个路径（例如/hello；/hello1,hello2,hello3；/hello/*）

```xml
<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>/*</url-pattern>默认请求路径，一进来就是这个（*前面不能有其他父路径）
  </servlet-mapping>

<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>*.xxx</url-pattern>用户自定义路径,后缀必须为.xxx
  </servlet-mapping>
```

**优先级问题：**<u>指定了的路径优先级最高</u>；找不到指定路径则走默认的处理请求



#### 4.5 ServletContext上下文

web容器在启动的时候，它会为每一个web程序都创建一个对应的ServletContext应用，它代表了当前的web应用。

##### 4.5.1 共享数据

我在这个servlet中保存的数据，可以在另外一个servlet中拿到（之前使用I/O操作，存取都在文件中）

- 第一个servlet保存数据

```Java
ServletContext context = this.getServletContext();
String username = "ldy";
context.setAttribute("username",username);//将一个数据保存在ServletContext中，是键值对的格式
```

- 另一个servlet拿到数据

```Java
ServletContext context = this.getServletContext();
String username = (String) context.getAttribute("username");//获取名为username的值

resp.setCharacterEncoding("utf-8");
resp.setContentType("text/html");
resp.getWriter().print(username);
```

- 配置servlet

```xml
<servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.daryl.servlet.HellloServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>

  <servlet>
    <servlet-name>getServlet</servlet-name>
    <servlet-class>com.daryl.servlet.getServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>getServlet</servlet-name>
    <url-pattern>/getServlet</url-pattern>
  </servlet-mapping>
```

##### 4.5.2 获取初始化参数

```java
ServletContext context = this.getServletContext();
String url = context.getInitParameter("url");//获取web.xml中的初始化信息
resp.getWriter().print(url);
```

web.xml中的初始化信息是：

```xml
<context-param>
    <param-name>url</param-name>
    <param-value>jdbc:mysql://localhost:3306/mybatis</param-value>
  </context-param>
```

##### 4.5.3 请求转发

```java
ServletContext context = this.getServletContext();
context.getRequestDispatcher("/hello").forward(req,resp);//请求转发，若进入本页面，但是真实走的是hello页面   路径没有变，里面内容是hello的内容

//重定向：路径和内容都会变
```

##### 4.5.4 读取资源文件

Properties类

- 在Java目录下新建properties
- 在resources目录下新建properties

发现都被打包到class路径下（classespath类路径）

前提是必须在web.xml中的build中配置好：

```xml
<build>
        <!--配置以下防止我们自己写在java和resources中的xml资源导出失败的问题-->
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <excludes>
                    <exclude>**/*.properties</exclude>
                    <exclude>**/*.xml</exclude>
                </excludes>
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

**读取操作**:需要一个文件流

```java
//将打包生成的资源文件变成流的形式，因为properties类就是处理流的形式
InputStream is = this.getServletContext().getResourceAsStream("/WEB-INF/classes/db.properties");
Properties properties = new Properties();//读取资源的类
properties.load(is);//加载资源文件
String username = properties.getProperty("username");//实现读取资源文件的操作
String password =properties.getProperty("password");
resp.getWriter().print(username+password);
```



**PrintWriter类**：用来创建一个<u>文件并向文本文件写入数据。可以理解为java中的文件输出，java中的文件输入则是java.io.File</u>。

```java
PrintWriter writer = resp.getWriter();//响应流  获取响应的输入流，返回PrintWriter对象
writer.print("Hello Servlet");
```



#### 4.6 HttpServletRequest

​		代表客户端的请求，用户通过http协议访问服务器，http请求中的所有信息会被封装到HttpServletRequest，通过这个HttpServletRequest方法获得客户端的所有信息。

1. 获取前端传递的参数

   - ```java
     	//后台接收中文乱码问题
       	req.setCharacterEncoding("utf-8");
       	//结束的时候也设置
       	resp.setCharacterEncoding("utf-8");
       
       	//req.getParameter()-----返回String
       	String username = req.getParameter("username");
         String password = req.getParameter("password");
         //req.getParameterValues()-----返回String[]数组
         String[] parameterValues = req.getParameterValues("hobbys");
     ```

2. 请求转发

   - ```java
     req.getRequestDispatcher("/hello").forward(req,resp);//转发的时候不需要写前面的路径（重定向的时候需要） "/"已经代表了当前的web应用
     ```

#### 4.7 HttpServletResponse

web服务器接收到的客户端的http请求，针对这个请求，分别创建一个代表请求的HttpServletRequest对象，和一个代表响应的HttpServletResponse对象。

- 如果要获取客户端”请求“过来的参数：找HttpServletRequest
- 如果要给客户端”响应“一些信息：找HttpServletResponse

响应中的方法分类：

1. 负责向浏览器发送数据的方法

   ```java
   ServletOutputStream getOutputStream() throws IOException;  //写入平常的流
   PrintWriter getWriter() throws IOException;  //写入中文
   ```

2. 负责向浏览器发送响应头的方法

3. 负责向浏览器发送响应状态码

常见应用：

1. 向浏览器输出消息
2. 下载文件/上传文件
   1. 获取下载文件的路径
   2. 下载的文件名
   3. 设置让浏览器支持下载我们需要的东西
   4. 获取下载文件的输入流
   5. 创建缓冲区
   6. 获取OutputStream对象
   7. 将FileOutputStream流写入到buffer缓冲区
   8. 使用OutputStream将缓冲区中的数据库输出到客户端

```java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//         1. 获取下载文件的路径
        String path = "E:\\MavenDaryl\\response\\target\\response\\WEB-INF\\classes\\1.png";
                /*this.getServletContext().getRealPath("/1.png");*/
        System.out.println(path);/*打印下载的文件路径*/
//         2. 下载的文件名
        String filename = path.substring(path.lastIndexOf("\\") + 1);/*截取/后面的名字，\\是为了转义，+1则只要后面的*/
//         3. 设置让浏览器支持下载(Content-disposition)我们需要的东西,中文文件名需要URLEncoder转码
        resp.setHeader("Content-disposition","attachment;filename"+ URLEncoder.encode(filename,"utf-8"));
//         4. 获取下载文件的输入流
        FileInputStream in = new FileInputStream(path);
//         5. 创建缓冲区
        int len = 0;
        byte[] buffer = new byte[1024];
//         6. 获取OutputStream对象
        ServletOutputStream out = resp.getOutputStream();
//         7. 将FileOutputStream流写入到buffer缓冲区;8. 使用OutputStream将缓冲区中的数据库输出到客户端
        while ((len = in.read(buffer)) > 0) {
            out.write(buffer,0,len);
        }
        in.close();
        out.close();
    }
```

3. 验证码功能

   需要用到Java的图片类，生成一个图片。

   ```java
   @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           //如何让浏览器3秒自动刷新一次
           resp.setHeader("refresh","3");
           //在内存中创建图片
           BufferedImage image = new BufferedImage(80,20,BufferedImage.TYPE_INT_RGB);
           //得到图片
           Graphics2D graphics = (Graphics2D) image.getGraphics();//相当于画笔
           //设置图片的背景颜色
           graphics.setColor(Color.white);//背景色
           graphics.fillRect(0,0,80,20);//位置大小
           //给图片写数据
           graphics.setColor(Color.BLUE);//画笔颜色
           graphics.setFont(new Font(null,Font.BOLD,20));
           graphics.drawString(makeNum(),0,20);//把随机数画上去
           //告诉浏览器，这个请求用图片的形式打开
           resp.setContentType("image/jpeg");
           //网站存在缓存，不让浏览器缓存
           resp.setDateHeader("expires",-1);
           resp.setHeader("Cache-Control","no-cache");
           resp.setHeader("Pragma","no-cache");
           //把图片写给浏览器
           ImageIO.write(image,"jpg",resp.getOutputStream());
       }
   
       //生成随机数
       private String makeNum() {
           Random random = new Random();
           String num = random.nextInt(9999999) + "";
           StringBuffer sb = new StringBuffer();
           for (int i = 0; i < 7-num.length(); i ++) {//保证随机数是7位，若不足7位则用0填充
               sb.append(0);
           }
           num = sb.toString() + num;
           return num;
       }
   ```

4. 实现重定向

   重定向：B收到客户端A请求后，通知A去访问资源C。

   ```Java
   resp.sendRedirect("/ImageServlet");
   ```

   面试题：

   ​	重定向和转发的区别？

   ​		“请求转发”的时候，url不会变化，307；

   ​		“重定向”的时候，<u>url会发生变化</u>,302。

   jsp页面：

   ```jsp
   <%--这里提交的路径需要找到项目的路径--%>
   <%--pageContext.request.contextPath代表当前的项目--%>
   <form action="${pageContext.request.contextPath}/login" method="get">
       用户名：<input type="text" name="username"> <br>
       密码：<input type="password" name="password"> <br>
       <input type="submit">
   ```

   Java接收请求代码：

   ```Java
   		//处理请求
           String username = req.getParameter("username");
           String password = req.getParameter("password");
           System.out.println(username+password);
           resp.sendRedirect("/sucess");//重定向到sucess页面
   ```



#### 4.8  Cookie--Session

##### 4.8.1会话

​	会话：用户打开浏览器，点击很多超链接，访问多个web资源，关闭浏览器。整个过程叫会话。

- 有状态会话：一个同学来过教室，下次再来，就会知道这个同学曾今来过。

  “服务器”如何证明“客户端”来过？

  1. 服务器给客户端一个信件（cookie），客户端下次访问服务端带上信件就可以。
  2. 服务器登记你来过了，下次你来的时候我来匹配你（session）

##### 4.8.2保存会话的两种技术

###### cookie

- 客户端技术(传信件)	（响应，请求）

###### session

- 服务器技术	利用这个技术可以保存用户的会话信息，将信息或数据放在session中。

##### 4.8.3 Cookie

1. 从请求中拿到cookie的信息

2. 服务器相应给客户端

   ```Java
   		//cookie 服务器端从客户端获取
           Cookie[] cookies = req.getCookies();//返回数组可能cookie有多个
           //判断cookie是否存在
           if (cookies != null) {
               //如果存在cookie
               out.print("上一次访问的时间是:");
               for (int i = 0; i< cookies.length; i ++) {
                   Cookie cookie = cookies[i];//获取每一个cookie
                   String name = cookie.getName();//获取cookie的名字  key
                   if (name.equals("cookieName")) {
                       //对比遍历的cookie的名字和我们cookie的名字，比较值是否相符
                       String value = cookie.getValue();   //获得cookie中的值  value
                       long l = Long.parseLong(value);//将字符串换成时间戳
                       Date date = new Date(l);//时间戳变成对象
                       out.write(date.toLocaleString());
                   }
               }
           }else {
               out.print("第一次访问");
           }
   
           //服务器给客户端响应/发送一个cookie
           Cookie cookie = new Cookie("cookieName", System.currentTimeMillis()+"");//+""是为了转换为字符串
           cookie.setMaxAge(24*60*60);		//设置cookie的有效期为1天
           resp.addCookie(cookie);		//响应给客户端一个cookie
   ```

   若new的cookie的值是中文，那么<font color = "yellowgreen">编码</font>需要写为`Cookie cookie = new Cookie("cookieName", URLEncoder.encode("刘德昱","utf-8"));`，<font color = "yellowgreen">解码</font>时写为`URLDecoder.decode(cookie.getValue(),"utf-8")`

   

   **一个网站的cookie是否存在上限？**

   - 一个cookie只能保存一个信息
   - 一个web站点（服务器）可以给浏览器发送多个cookie（浏览器上限大约为300个cookie）
   - 每个站点最多存放20个cookie
   - cookie的大小有限制4kb

   **删除cookie**

   - 不设置有效期，关闭浏览器，自动失效
   - 设置有效期时间为0	`cookie.setMaxAge(0);`
   
   

##### 4.8.4 Session（重点）

**什么是session？**

- 服务器会给每个浏览器/用户创建一个session对象
- 一个session独占一个浏览器，只要浏览器没关闭，这个session就存在
- 用户登录后，整个网站他都可以访问------->保存用户信息



使用场景：

- 保存一个登录用户的信息；
- 购物车信息；
- 在整个项目网站中经常会使用的数据；

```java
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        //得到session
        HttpSession session = req.getSession();

        //给session存数据
        session.setAttribute("sessionName",new Person("刘德昱",24));
        //获取session的ID
        String id = session.getId();
        //判断session是否为新创建的
        if (session.isNew()) {
            resp.getWriter().write("session创建成功，id为"+id);
        }else {
            resp.getWriter().write("session已经创建,ID为"+id);
        }

        /*在session创建的时候自动做了一些事情
        * Cookie cookie = new Cookie("JSESSIONID",id);
        * resp.addCookie(cookie);
        * */
    }
===========================================================================================================
		

		//得到session
        HttpSession session = req.getSession();
        Person sessionName = (Person) session.getAttribute("sessionName");
        System.out.println(sessionName.toString());//转为string类型打印

        //手动注销session
        req.removeAttribute("sessionName");
        session.invalidate();


============================================================================================================
    	//自动注销会话session
    	在web.xml中配置
    	<!--设置session默认的失效时间-->
  		<session-config>
    		<!--一分钟过后session失效（以分钟为单位）-->
    		<session-timeout>1</session-timeout>
  		</session-config>
```



**session与cookie的区别？**

1. Cookie是把用户的数据写给用户的浏览器，浏览器保存
2. Session是把用户的数据写到用户独占的Session中，服务器保存（保存重要的信息，减少服务器资源的浪费）
3. Session对象由服务器创建



#### 5、 JSP

Java服务器端页面，与servlet一样，用于动态web技术。

##### 5.1 JSP原理

浏览器向服务器发送请求，无论访问什么资源，其实都在访问Servlet

<font color = "yellowgreen">JSP页面最终也会转换为Java类；其本质上就是一个servlet。**以下是工作中jsp转换为的java文件中的代码：**</font>

![image-20201014210222845](C:\Users\dn3\AppData\Roaming\Typora\typora-user-images\image-20201014210222845.png)

```Java
public void _jspInit() {//初始化
  }

  public void _jspDestroy() {//销毁
  }

  public void _jspService(final javax.servlet.http.HttpServletRequest request, final javax.servlet.http.HttpServletResponse response)
      throws java.io.IOException, javax.servlet.ServletException {
      ----------------//判断请求-------------------------

    if (!javax.servlet.DispatcherType.ERROR.equals(request.getDispatcherType())) {
      final java.lang.String _jspx_method = request.getMethod();
      if ("OPTIONS".equals(_jspx_method)) {
        response.setHeader("Allow","GET, HEAD, POST, OPTIONS");
        return;
      }
      if (!"GET".equals(_jspx_method) && !"POST".equals(_jspx_method) && !"HEAD".equals(_jspx_method)) {
        response.setHeader("Allow","GET, HEAD, POST, OPTIONS");
        response.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, "JSP 只允许 GET、POST 或 HEAD。Jasper 还允许 OPTIONS");
        return;
      }
    }
	//---------------------------内置了一些对象--------------------------
    final javax.servlet.jsp.PageContext pageContext;//页面上下文
    javax.servlet.http.HttpSession session = null;//session
    final javax.servlet.ServletContext application;//applicationContext
    final javax.servlet.ServletConfig config;//config
    javax.servlet.jsp.JspWriter out = null;//out
    final java.lang.Object page = this;//page，当前
      //------------------------输出页面前的代码-------------------------
      try {
      response.setContentType("text/html");
      pageContext = _jspxFactory.getPageContext(this, request, response,
      			null, true, 8192, true);
      _jspx_page_context = pageContext;
      application = pageContext.getServletContext();
      config = pageContext.getServletConfig();
      session = pageContext.getSession();
      out = pageContext.getOut();
      _jspx_out = out;
	  //以上的这些对象可以在JSP页面中获得，用${xxx}来获取
      
      out.write("<html>\n");
      out.write("<body>\n");
      out.write("<h2>Hello World!</h2>\n");
      out.write("</body>\n");
      out.write("</html>\n");
    } catch (java.lang.Throwable t) {
      if (!(t instanceof javax.servlet.jsp.SkipPageException)){
        out = _jspx_out;
        if (out != null && out.getBufferSize() != 0)
          try {
            if (response.isCommitted()) {
              out.flush();
            } else {
              out.clearBuffer();
            }
          } catch (java.io.IOException e) {}
        if (_jspx_page_context != null) _jspx_page_context.handlePageException(t);
        else throw new ServletException(t);
      }
    } finally {
      _jspxFactory.releasePageContext(_jspx_page_context);
    }
  }
```

![image-20201014211351222](C:\Users\dn3\AppData\Roaming\Typora\typora-user-images\image-20201014211351222.png)

JSP中：

Java代码会原封不动的输出

HTML代码会被转换为以下格式输出：

```html
out.write("<html>\r\n");
```



##### 5.2 JSP基础语法及指令

以下代码最后会生成为Java文件，且都是**在_jspService()函数中**：

###### JSP表达式：<%= %>

```jsp
1、JSP表达式：<%= 变量或者表达式%>		<%--在jsp中使用写Java代码,称为JSP表达式；用于将程序的输出输出客户端--%>
<%= new java.util.Date()%>
```

###### JSP脚本片段：<% %>

```jsp
2、JSP脚本片段：<% %>
<%
    int sum = 0;
    for (int i = 0; i <=10 ; i++) {
        sum+=i;
    }
    out.println("<h1>Sum="+sum+"</h1>");
%>

3、在Java代码中嵌入HTML元素
<%
    for (int i =0;i<4;i++) {
%>
<h1>hello  <%=i%></h1>
<%
    }
%>
```

实现在`_jspService()`**函数外**写代码，成为全局变量、函数：

###### JSP声明  <%!     %>

```jsp
<%!
    private int globalVar = 0;
	public void function(){
        
    }
%>
```

###### EL表达式  ${}

也可以用${}代替<%%>。



##### 5.3 JSP指令

`<%@ page ......%>`

```jsp
<%@ page errorPage="error/500.jsp"%>  	<!--含义是定制错误页面。错误时显示的页面是error文件夹下的500.jsp，不再是系统默认的500错误页面-->

也可以在web.xml中配置：
<error-page>
    <error-code>404</error-code>
    <location>/error/404.jsp</location>
</error-page>
```

`<%@ include ......%>`可以用于包含公共的头部和底部

```jsp
//@include将两个页面合二为一,将二者内容都取出来放在函数中，如果不同的页面有相同的变量就会报错
<body>
<%@include file="common/header.jsp"%>
<h1>网页主体</h1>
<%@include file="common/footer.jsp"%>
    
也可以用（用的更多）：
    //jsp:include拼接页面，本质还是三个页面，变量相同也没关系
    <jsp:include page="common/header.jsp"/>
    <h1>网页主体</h1>
	<jsp:include page="common/footer.jsp"/>
</body>
```

##### 5.4  9大内置对象

- PageContext     存数据
- Resquest     存数据
- Response
- Session    存数据
- Application     【相当于servletContext】存数据
- config    【servletConfig】
- out
- page
- exception

```jsp
<%
    //存数据
    pageContext.setAttribute("name1","刘德昱");//pageContext保存的数据只在“一个页面中有效”
    request.setAttribute("name2","刘德昱");//request保存的数据只在“一个请求中有效，请求转发会携带这个数据”
    session.setAttribute("name3","刘德昱");//保存的数据只在“一个会话中有效，从打开浏览器到关闭游览器”
    application.setAttribute("name4","刘德昱");//保存的数据库只在“服务器中有效，从打开服务器到关闭服务器”
%>


<%
    //找到数据   可以通过getAttribute，现在通过findAttribute寻找方式来实现
    String name1 =  (String) pageContext.findAttribute("name1");
    String name2 =  (String) pageContext.findAttribute("name2");
    String name3 =  (String) pageContext.findAttribute("name3");
    String name4 =  (String) pageContext.findAttribute("name4");
%>

<%--EL表达式取值--%>
<h3>${name1}</h3>
<h3>${name2}</h3>
<h3>${name3}</h3>
<h3>${name4}</h3>

前端实现转发：
pageContext.forward("/index.jsp");
后台实现转发：
request.getRequestDispatcher("/index.jsp").forward(requset,response);
```

request：客户端向服务器发送请求，产生的数据用户看完就没用了；比如：新闻

session：服务器向服务器发送请求，产生的数据用户用完一会还有用；比如：购物车

application：服务器向服务器发送请求，产生的数据一个用户用完了，其他用户还能使用；比如：聊天记录



##### 5.5 JSP标签、 JSTL标签、EL表达式

```xml
		<!-- jstl表达式的依赖 -->
        <dependency>
            <groupId>javax.servlet.jsp.jstl</groupId>
            <artifactId>jstl-api</artifactId>
            <version>1.2</version>
        </dependency>
        <!-- 表达式需要一些标签，下面是标签库 -->
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>
```

EL表达式：	${}

- 获取数据
- 执行运算
- 获取web开发的常用对象



###### **JSP标签**

```jsp
请求转发到jsp2.jsp页面，而且带两个参数（网址上会显示为jsp2.jsp?name=liudeyu&age=11）
<jsp:forward page="/jsp2.jsp">
    <jsp:param name="name" value="liudeyu"></jsp:param>
    <jsp:param name="age" value="11"></jsp:param>
</jsp:forward>
```



###### **JSTL标签**

是为了弥补HTML标签的不足；

1. 需要引入jstl核心标签库：

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

![image-20201017162135635](C:\Users\dn3\AppData\Roaming\Typora\typora-user-images\image-20201017162135635.png)

2.tomcat中或许没有jstl和其依赖的standard包，需要自己引入

```jsp
案例：
//1   判断
<form action="pageContext.jsp" method="get">
    <input type="text" name="username" value="${param.username}">
    <input type="submit" value="登录">
</form>
<c:if test="${param.username=='admin'}" var="isAdmin">   //test条件   var用于存储条件结果的变量  scopevar属性的作用域
    <c:out value="登录成功"/>
</c:if>
<c:out value="${isAdmin}"/>		//输出true或false
<%
    //一般写法
    if (request.getParameter("username").equals("admin")){
        out.print("登录成功");
    }
%>
//2   存储数据
<c:set var="salary" scope="session" value="${2000*2}"/>   //定义一个变量叫salary，作用域实在session范围内，值为2000*2
//3   when判断
<c:choose>
    <c:when test="${salary <= 0}">
       太惨了。
    </c:when>
    <c:when test="${salary > 1000}">
       不错的薪水，还能生活。
    </c:when>
    <c:otherwise>
        什么都没有。
    </c:otherwise>
</c:choose>

//4   便利数据
<%
    ArrayList<String> people = new ArrayList<>();
    people.add(1,"张三");
    people.add(2,"李四");
    people.add(3,"王五");
    request.setAttribute("list",people);//用一个名字为list的数组作为请求
%>
<c:forEach var="people" items="${list}">//items	要被循环的信息    var	代表当前条目的变量名称   相当于foreach(var people:list)
    <c:out value="${people}"/><br>
</c:forEach>
```



##### 5.6  JavaBean

**实体类**

特性写法：

1. 必须有一个无参构造
2. 属性必须私有化
3. 必须有对应的get/set方法

<font color ="yellogreen">实体类一般用于和数据库的字段做映射，一一对应！</font>

ORM（对象关系映射）：

- 数据库中的“表”------->类
- 字段------->属性
- 行记录------>类的对象

**People表**

| id   | name  | age  | address |
| ---- | ----- | ---- | ------- |
| 1    | 张三1 | 11   | 武汉    |
| 2    | 张三2 | 12   | 武汉    |
| 3    | 张三3 | 13   | 武汉    |

```Java
class People{		//people表对应的people实体类
    private int id;
    private String name;
    private int age;
    private String address;
}

class A {
    new People(4,"张三4",15,"武汉");
}
```



##### 5.7 MVC三层架构

含义：Model模型     View视图     Controller控制器

![image-20201019130322280](C:\Users\dn3\AppData\Roaming\Typora\typora-user-images\image-20201019130322280.png)

Model

- 业务处理：业务逻辑（Service）
- 数据持久层CRUD（Dao）

View

- 展示数据
- 提供链接发起servlet请求

Controller

- 接收用户的请求（request：请求参数、session信息）
- 交给业务层处理对应的代码
- 控制视图的跳转

```
例子：
登录----->Controller接收用户的登录请求----->处理用户的请求（获取用户登录的参数，username，password）------>交给业务层Model处理登录业务（判断用户名密码是否正确：事务）---->Dao层查询用户名、密码是否正确----->数据库
```



##### 5.8 过滤器（重点）

Filter：过滤器，用于过滤网站的数据（例如：处理中文乱码、登录验证...）

实现步骤：

1. 导包：过滤需要实现implements Filter接口（javax.servlet下的）

2. 在过滤器doFilter函数中写代码

   1. ```java
      //初始化   ：服务器打开则执行
          public void init(FilterConfig filterConfig) throws ServletException {
      
          }
      
          //filterChain过滤链,可以有很多个过滤器-----------实现放行的作用
          /*
          * 1、过滤器中的所有代码，在过滤特定请求的时候都会执行
          * 2、必须让过滤器继续执行
          *   filterChain.doFilter(servletRequest,servletResponse);//让我们的请求继续走，若不写这句，程序到这就会停止
          * */
          public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
              servletRequest.setCharacterEncoding("utf-8");
              servletResponse.setCharacterEncoding("utf-8");
              servletResponse.setContentType("text.html;charset=utf-8");
      
              System.out.println("执行前。。。");
              filterChain.doFilter(servletRequest,servletResponse);//让我们的请求继续走，若不写这句，程序到这就会停止
              System.out.println("执行后。。。");
          }
      
          //销毁  ：服务器关闭就销毁
          public void destroy() {
      
          }
      ```

3. 在web.xml中配置

   1. ```xml
      	<filter>
              <filter-name>CharacterEncodingFilter</filter-name>
              <filter-class>com.daryl.filter.CharacterEncodingFilter</filter-class>
          </filter>
          <filter-mapping>
              <filter-name>CharacterEncodingFilter</filter-name>
              <url-pattern>/servlet/*</url-pattern><!--只要是/servlet文件下的任何请求都会经过这个过滤器-->
          </filter-mapping>
      ```

      

##### 5.9 监听器

Listener：实现一个监听器的接口；

在web.xml中配置监听器

```xml
<listener>
    <listener-class>com.daryl.listener.OnlineListener</listener-class>
</listener>
```

Java中的代码：实现监听在线人数---->统计网站在线人数：通过监听session，一个session对应一个人

```Java
public class OnlineListener implements HttpSessionListener {
    //session创建的监听    创建一次就会触发监听
    public void sessionCreated(HttpSessionEvent se) {
        ServletContext ctx = se.getSession().getServletContext();
        Integer onlineCount = (Integer) ctx.getAttribute("OnlineCount");//实现的是session创建后获取OnlineCount的session对象

        if (onlineCount == null) {
            onlineCount = new Integer(1);
        }else {
            int count = onlineCount.intValue();
            onlineCount = new Integer(count++);
        }

        ctx.setAttribute("OnlineCount",onlineCount);
    }

    //session销毁的监听
    public void sessionDestroyed(HttpSessionEvent se) {
        ServletContext ctx = se.getSession().getServletContext();
        Integer onlineCount = (Integer) ctx.getAttribute("OnlineCount");//实现的是session创建后获取OnlineCount的session对象

        if (onlineCount == null) {
            onlineCount = new Integer(0);
        }else {
            int count = onlineCount.intValue();
            onlineCount = new Integer(count--);
        }

        ctx.setAttribute("OnlineCount",onlineCount);
    }
}
```



##### 5.10 过滤器、监听器常用功能

- 登录拦截：用户注册之后才能进入主页，注销之后就不能进入主页了



##### 5.11 JDBC回顾

Java连接数据库。（Java database connecttion）

![image-20201026194824978](C:\Users\dn3\AppData\Roaming\Typora\typora-user-images\image-20201026194824978.png)

需要jar包的支持：

- Java.sql
- javax.sql
- mysql-connect-Java...连接驱动，必须要导入

```xml
		<!--连接数据库驱动配置-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.19</version>
        </dependency>
```

数据库表的操作：

```sql
create table users(//创建表
    id INT PRIMARY KEY,
    'name' varchar(20),
    'password' varchar(20)
);
//插入表
INSERT INTO users(id,'name','password')
VALUES(1,'张三','123456');

```

JDBC固定操作：

```Java
public static void main(String[] args) throws ClassNotFoundException, SQLException {
        String url = "jdbc:mysql://localhost:3306/数据库名?useUnicode=true&characterEncoding=utf-8&useSSL=true";
        String username = "root";
        String password = "123456";
        //1、加载驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2、连接数据库     connection代表数据库，DriverManager驱动管理，管理数据库
        Connection connection = DriverManager.getConnection(url, username, password);
        //3、向数据库发送sql的对象,用statement做增删改查
        Statement statement = connection.createStatement();
        //4、编写sql
        String sql = "select * from users";//查
    	String sql1 = "delete * from users where id = 4";//删
        //5、执行sql,返回一个结果集
        ResultSet rs = statement.executeQuery(sql);//查询
    	int i = statement.executeUpdate(sql1);//受影响的行数；增删改都用executeUpdate
        
        while (rs.next()) {
            System.out.println("id="+rs.getObject("id"));
            System.out.println("id="+rs.getObject("username"));
            System.out.println("id="+rs.getObject("passsword"));
        }
        //6、关闭连接，释放资源（先开的后关）
        rs.close();
        statement.close();
        connection.close();
    }
```

预编译sql：

```Java
public static void main(String[] args) throws ClassNotFoundException, SQLException {
        String url = "jdbc:mysql://localhost:3306/数据库名?useUnicode=true&characterEncoding=utf-8&useSSL=true";
        String username = "root";
        String password = "123456";
        //1\加载驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2\连接数据库     connection代表数据库
        Connection connection = DriverManager.getConnection(url, username, password);
        //3\编写sql
        String sql = "insert into 数据库名(id,username,password) values (?,?,?)";
        //4、预编译
        PreparedStatement preparedStatement = connection.prepareStatement(sql);//在这会执行sql，sql中若有参数
        preparedStatement.setInt(1,1);//给第1个占位符？的值赋值为1
        preparedStatement.setString(2,"liudeyu");//给第2个占位符？的值赋值为liudeyu
        preparedStatement.setString(3,"123456");//给第3个占位符？的值赋值为123456
        //5执行sql
        int i = preparedStatement.executeUpdate();
        if (i > 0) {
            System.out.println("插入成功");
        }
        //6\关闭连接，释放资源（先开的后关）
        preparedStatement.close();
        connection.close();
    }
```

###### 5.11.1 事务

要么都成功，要么都失败！(ACID原则)

```java
1、开启事务	start transaction;
2、事务提交	commit()
3、事务回滚	rollback()
4、关闭事务
    
例子：转账
    A:1000		B:1000
    A(900)---(100)-->B(1100)//(实现若网络断开，则要不100给了B之后双方都变，要不都还是1000)
```



Junit单元测试：

依赖：

```xml
<!--单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>
```

使用：

```Java
	@Test			
    public void test(){ //使用@Test注解（只在方法上有效）  就不用再使用main方法来调用了，可以直接执行函数进行测试
        System.out.println("Hello");
    }
```

**搭建环境测试事务**：

```Java
	@Test
    public void test() { //使用@Test  就不用再使用main方法来调用了，可以直接执行函数进行测试
        String url = "jdbc:mysql://localhost:3306/数据库名?useUnicode=true&characterEncoding=utf-8&useSSL=true";
        String username = "root";
        String password = "123456";

        Connection connection = null;
        try {
            //1 加载驱动
            Class.forName("com.mysql.jdbc.Driver");

            //2 连接数据库     connection代表数据库
            connection = DriverManager.getConnection(url, username, password);
            //3 通知数据库开启事务   false是开启
            connection.setAutoCommit(false);
            String sql = "update account set money = money-100 where name = 'A";
            connection.prepareStatement(sql).executeUpdate();
            //制造错误
            int i = 1/0;
            String sql2 = "update account set money = money+100 where name = 'B";
            connection.prepareStatement(sql2).executeUpdate();
            connection.commit();//以上两条都执行成功则提交事务
            } catch (Exception e) {
            try {//如果出现异常就通知数据库“回滚”操作
                connection.rollback();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
            e.printStackTrace();
            }
    }
```

