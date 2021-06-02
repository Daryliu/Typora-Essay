### Servelt

Servlet通常称为服务器端小程序，是运行在服务器端的程序，用于处理及响应客户的请求。

Servlet是个特殊的java类，继承于**HttpServlet**。客户端通常只有GET和POST两种请求方式，Servlet为了响应这两种请求，必须重写doGet()和doPost()方法。

#### 文件目录结构

##### WEB-INF下xml配置 

**web.xml文件是用来配置：欢迎页、servlet、filter等的。当你的web工程没用到这些时，你可以不用web.xml文件来配置你的web工程。**

/WEB-INF/web.xml：Web应用程序配置文件，描述了 servlet 和其他的应用组件配置及命名规则。

```java
<description>：为Servlet指定一个文本描述。
<display-name>：为Servlet提供一个简短的名字被某些工具显示。
<icon>：为Servlet指定一个图标，在图形管理工具中表示该Servlet。
```

```java
<servlet>中的属性参数：
	<servlet-name>：用来定义servlet的名称，该名称在整个应用中必须是惟一的。
	<servlet-class>：用来指定servlet的完全限定的名称。即实现类的全名
    <jsp-file>：用来指定应用中JSP文件的完整路径。这个完整路径必须由/开始。
    <init-param>用来定义初始化参数，可有多个init-param。在servlet类中通过ServletConfig对象传入init函数，通过- getInitParamenter(String name)方法访问初始化参数
例如使用<init-param>来初始化数据库连接参数
        案例：
 public void init(ServletConfig config) throws SevletException{
	super(config);
	String driver = config.getInitParameter("driver");
	String url = config.getInitParameter("url");
	String username = config.getInitParameter("username");
	String passwd = config.getInitParameter("passwd");
	try{
		Class.forName(driver).newInstance();
		this.conn = DriverManager.getConnection(url, username, passwd);
		System.out.println("Connection successful...");
	} catch(SQLExceprion se){
		System.out.println("se");
	} catch(Exception e){
		e.printStackTrace():
	}	
}
此时servlet配置为：
	<servlet>
    <servlet-name>myServlet</servlet-name>
    <servlet-class>*.myservlet</servlet-class>
    <init-param>
        <param-name>driver</param-name>
        <param-value>com.mysql.jdbc.Driver</param-value>
    </init-param>
    <init-param>
        <param-name>url</param-name>
        <param-value>jdbc:mysql://localhost:3306/myDatabase</param-value>
    </init-param>
    <init-param>
        <param-name>username</param-name>
        <param-value>tang</param-value>
    </init-param>
    <init-param>
        <param-name>passwd</param-name>
        <param-value>whu</param-value>
    </init-param>
</servlet>
```

```java
<servlet-mapping>中的属性参数
    <servlet-name>：Servlet的名字，唯一性和一致性，与<servlet>元素中声明的名字一致。
	<url-pattern>：指定相对于Servlet的URL的路径。该路径相对于web应用程序上下文的根路径。	<servlet-mapping>将URL模式映射到某个Servlet，即该Servlet处理的URL。
```

/WEB-INF/lib/：存放web应用需要的各种JAR文件，放置仅在这个应用中要求使用的jar文件,如数据库驱动jar文件。

#### servlet request获取参数

1. 在前端页面创建“”登录文件后

   action="login" 标题会提交到login路径，login路径在后续步骤会映射到LoginServlet
   method="post" post方式表示提交的密码信息在浏览器地址栏看不到

2. 后台java中创建LoginServlet

   因为浏览器中的**form的method是post**,所以LoginServlet需要提供一个**doPost**方法

   在doPost方法中，通过**request.getParameter** 根据name取出对应的账号和密码

3. 在web.xml中新增LoginServlet的映射

#### Servlet response返回响应

根据浏览器提交的账号密码返回登录成功或者失败。

```java
PrintWriter pw = response.getWriter();//获取输出流
pw.println(html);//打印输出html
```

#### Servlet调用流程

[详细流程见网站](https://how2j.cn/k/servlet/servlet-flow/775.html)

<img src="D:\我与秋风皆过客\Typora-Essay\Java\photo\servlet流程.png" alt="servlet流程" style="zoom:80%;" />



#### Servlet跳转

##### 1、服务端跳转 request.getRequestDispatcher

**RequestDispatcher**.forward()是在**服务器端运行**;<u>服务器内部跳转，地址栏信息不变，只能跳转到web应用内的网页。</u>

<font color="greenyellow">在服务器内进行访问跳转,再把页面内容发送给浏览器</font>

request.getRequestDispatcher("success.html").forward(request, response);实现跳转

```java
public class LoginServlet extends HttpServlet {
 
    private static final long serialVersionUID = 1L;
 
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
 
        String name = request.getParameter("name");
        String password = request.getParameter("password");
        //如果账号密码正确
        if ("admin".equals(name) && "123".equals(password)) {
            //则在服务端进行跳转，跳转的路径依然是/login路径，而不是success.html
            request.getRequestDispatcher("success.html").forward(request, response);//此方法对浏览器而言是透明的
        }
    }
}
```

##### 2、客户端跳转 response.sendRedirect()

<font color="greenyellow">服务器发送消息给浏览器,让其访问页面</font>

HttpServlet**Response.****sendRedirect()**是通过**向客户浏览器发送命令来完成**,<u>页面重定向，地址栏信息改变，可以跳转到任意网页。</u>

```java
public class LoginServlet extends HttpServlet {
 
    private static final long serialVersionUID = 1L;
 
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
 
        String name = request.getParameter("name");
        String password = request.getParameter("password");
 
        if ("admin".equals(name) && "123".equals(password)) {
            request.getRequestDispatcher("success.html").forward(request, response);//请求转发，前后页面共享一个request
        }
        else{
            //跳转至浏览器,网址显示的是fail.html
            response.sendRedirect("fail.html");//重新定向，前后页面不是一个request
        }
    }
}
```

#### Servlet request方法

###### 常见方法

**request.getRequestURL():** 浏览器发出请求时的完整URL，包括协议 主机名 端口(如果有)"
       **request.getRequestURI():** 浏览器发出请求的资源名部分，去掉了协议和主机名"
       **request.getQueryString():** 请求行中的参数部分，只能显示以get方式发出的参数，post方式的看不到
       **request.getRemoteAddr():** 浏览器所处于的客户机的IP地址
       **request.getRemoteHost():** 浏览器所处于的客户机的主机名
       **request.getRemotePort():** 浏览器所处于的客户机使用的网络端口
       **request.getLocalAddr():** 服务器的IP地址
       **request.getLocalName():** 服务器的主机名
       **request.getMethod():** 得到客户机请求方式一般是GET或者POST

###### 获取参数

**request.getParameter()**: 是常见的方法，用于获取<u>单值的参数</u>
	   **request.getParameterValues():** 用于获取具有<u>多值的参数，比如注册时候提交的 "hobits"，可以是多选的</u>。
       **request.getParameterMap():** 用于<u>遍历所有的参数，并返回Map类型</u>。

###### 获取头信息

**request.getHeader()** 获取浏览器传递过来的头信息。
比如getHeader("user-agent") 可以获取浏览器的基本资料，这样就能判断是firefox、IE、chrome、或者是safari浏览器
       **request.getHeaderNames()** 获取浏览器所有的**头信息名称**，根据头信息名称就能遍历出所有的头信息

###### 服务器传参

setAttribute和getAttribute可以用来在进行服务端跳转的时候，**在不同的Servlet之间进行数据共享**

#### Servlet response方法

###### 设置响应内容

PrintWriter pw= **response.getWriter();**
       通过response.getWriter(); 获取一个PrintWriter 对象
       可以使用println(),append(),write(),format()等等方法设置返回给浏览器的html内容。

###### 设置相应格式

response.setContentType("text/html");表示设置返回给浏览器的响应格式是html格式的.

"text/html" 是即格式 ，在[request获取头信息](https://how2j.cn/k/servlet/servlet-request/555.html#step1609) 中对应的request.getHeader("accept").

###### 设置响应编码

两种方式都需要在response.getWriter<font color="greenyellow">**调用之前执行**</font>才能生效。

```java
response.setContentType("text/html; charset=UTF-8");//不仅发送到浏览器的内容会使用UTF-8编码，而且还通知浏览器使用UTF-8编码方式进行显示。所以总能正常显示中文

 response.setCharacterEncoding("UTF-8");//仅仅是发送的浏览器的内容是UTF-8编码的，至于浏览器是用哪种编码方式显示不管
```

###### 301或302客户端跳转

<font color="bluegreen">区别</font>主要在搜索引擎对页面排名的时候有影响

302 表示临时跳转

```java
response.sendRedirect("fail.html");
```

 301 表示永久性跳转

```java
response.setStatus(301);
response.setHeader("Location", "fail.html");
```

###### 设置不使用缓存

使用缓存可以加快页面的加载，降低服务端的负担。但是也可能看到过时的信息，可以通过如下手段通知浏览器不要使用缓存。

```java
response.setDateHeader("Expires",0 );
response.setHeader("Cache-Control","no-cache");
response.setHeader("pragma","no-cache");
```

#### Servlet上传文件

前端页面:

1、method必须是**post**,加上**enctype="multipart/form-data"** 表示提交的数据是二进制文件;

2、需要提供属性类型为file**type="file"** 的字段进行上传

后台Servlet:

1、第三方的jar包，**commons-io-1.4.jar**和**commons-fileupload-1.2.2.jar**

```java
public class UploadPhotoServlet extends HttpServlet {
  
    public void doPost(HttpServletRequest request, HttpServletResponse response) {
  
        String filename = null;
        try {
            //是创建FileItem 对象的工厂
            //1创建一个解析器工厂;创建DiskFileItemFactory对象，设置缓冲区大小和临时文件目录
            DiskFileItemFactory factory = new DiskFileItemFactory();
            ////得到解析器
            //使用DiskFileItemFactory 对象创建ServletFileUpload对象，并设置上传文件的大小限制
            ServletFileUpload upload = new ServletFileUpload(factory);
            // 设置上传文件的大小限制为1M
            factory.setSizeThreshold(1024 * 1024);
             
            List items = null;
            try {
                items = upload.parseRequest(request);//该方法可以将通过表单中每一个HTML标签提交的数据封装成一个FileItem对象，然后以List列表的形式返回。使用该方法处理上传文件简单易用。
            } catch (FileUploadException e) {
                e.printStackTrace();
            }
            //对list进行迭代，每迭代一个FileItem对象，调用其isFormField方法判断是否是上传文件
            Iterator iter = items.iterator();
            while (iter.hasNext()) {
                FileItem item = (FileItem) iter.next();
                // 判断输入的类型,是文件时执行
                if (!item.isFormField()) {
  
                    // 根据时间戳创建头像文件
                    filename = System.currentTimeMillis() + ".jpg";
                     
                    //通过getRealPath获取上传文件夹，如果项目在e:/project/j2ee/web,那么就会自动获取到 e:/project/j2ee/web/uploaded
                    String photoFolder =request.getServletContext().getRealPath("uploaded");
                     
                    File f = new File(photoFolder, filename);//获取文件位置和文件名称
                    f.getParentFile().mkdirs();//创建文件夹,以免报错
  
                    // 通过item.getInputStream()获取浏览器上传的文件的输入流,从而读取上传数据
                    InputStream is = item.getInputStream();
  
                    // 复制文件
                    FileOutputStream fos = new FileOutputStream(f);
                    byte b[] = new byte[1024 * 1024];
                    int length = 0;
                    while (-1 != (length = is.read(b))) {
                        fos.write(b, 0, length);
                    }
                    fos.close();
  
                } else {
                    System.out.println(item.getFieldName());
                    String value = item.getString();
                    value = new String(value.getBytes("ISO-8859-1"), "UTF-8");
                    System.out.println(value);
                }
            }
             
            String html = "<img width='200' height='150' src='uploaded/%s' />";
            response.setContentType("text/html");
            PrintWriter pw= response.getWriter();
            pw.format(html, filename);
             
        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

