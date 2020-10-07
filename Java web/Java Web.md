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

























