## Spring MVC

[Spring MVC]: https://www.kuangstudy.com/zl/ssm#1381881096144203777

Spring MVC的特点：

1. 轻量级，简单易学
2. 高效 , 基于请求响应的MVC框架
3. 与Spring兼容性好，无缝结合
4. 约定优于配置
5. 功能强大：RESTful、数据验证、格式化、本地化、主题等
6. 简洁灵活

<font color = "blue">Spring的web框架围绕**DispatcherServlet** [ 调度所有的Servlet ] 设计。</font>DispatcherServlet的作用是**<u>将请求分发到不同的处理器，随即不用在配置每一个servlet，只需要配置DispatcherServlet即可！</u>**

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudyc49f3d6f-e0c6-4228-9bd7-6a40400c3bd4.png)

当发起请求时被前置的控制器拦截到请求，根据请求参数生成代理请求，找到请求对应的实际控制器，控制器处理请求，创建数据模型，访问数据库，将模型响应给中心控制器，控制器使用模型与视图渲染视图结果，将结果返回给中心控制器，再将结果返回给请求者。

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy00854e07-7eac-476c-a9dd-dcebb7ac0b89.png)

### 2.1、SpringMVC执行原理

![img](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy0214fd0a-0df0-4910-a467-5b7d61712868.png)

图为SpringMVC的一个较完整的流程图，实线表示SpringMVC框架提供的技术，不需要开发者实现，虚线表示需要开发者实现。

**简要分析执行流程**

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求。

我们假设请求的url为 : http://localhost:8080/SpringMVC/hello

**如上url拆分成三部分：**

[http://localhost:8080服务器域名](http://localhost:8080服务器域名/)

SpringMVC部署在服务器上的web站点

hello表示控制器

通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器。

1. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url查找Handler。
2. HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器为：hello。
3. HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。
4. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。
5. Handler让具体的Controller执行。
6. Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。
7. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。
8. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名。
9. 视图解析器将解析的逻辑视图名传给DispatcherServlet。
10. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。
11. 最终视图呈现给用户。

### 异常解决

1. **可能遇到的问题：访问出现404，排查步骤：**
   1. 查看控制台输出，看一下是不是缺少了什么jar包。
   2. 如果jar包存在，显示无法输出，就在IDEA的项目发布中，添加lib依赖！
   3. 重启Tomcat 即可解决！



#### ServletAPI

通过设置ServletAPI , 不需要视图解析器 .

1、通过HttpServletResponse进行输出

2、通过HttpServletResponse实现重定向

3、通过HttpServletResponse实现转发

```java
@Controller
public class ResultGo {

   @RequestMapping("/result/t1")
   public void test1(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
       rsp.getWriter().println("Hello,Spring BY servlet API");
  }

   @RequestMapping("/result/t2")
   public void test2(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
       rsp.sendRedirect("/index.jsp");
  }

   @RequestMapping("/result/t3")
   public void test3(HttpServletRequest req, HttpServletResponse rsp) throws Exception {
       //转发
       req.setAttribute("msg","/result/t3");
       req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,rsp);
  }

}
```

#### SpringMVC

**通过SpringMVC来实现转发和重定向 - 无需视图解析器；**

测试前，需要将视图解析器注释掉

```java
@Controller
public class ResultSpringMVC {
   @RequestMapping("/rsm/t1")
   public String test1(){
       //转发
       return "/index.jsp";
  }

   @RequestMapping("/rsm/t2")
   public String test2(){
       //转发二
       return "forward:/index.jsp";
  }

   @RequestMapping("/rsm/t3")
   public String test3(){
       //重定向
       return "redirect:/index.jsp";
  }
}
```

##### @RequestBody

在Spring MVC的Controller层使用@RequestBody接收Content-Type为application/json的数据时，默认**<u>支持<font color = "red">Map方式</font>和<font color = "red">对象方式</font>参数</u>**

```Java
@RequestMapping(value = "/{code}/saveUser", method = RequestMethod.POST)
    @ResponseBody
    public JsonResult saveUser(@PathVariable("code") Integer code, @RequestBody Map<String, Object> datas,@RequestBody User user) {
    //其中datas是map类型、user是User对象
    }
```

##### @ApiModelProperty()

@ApiModelProperty()用于方法，字段； 表示对model属性的说明或者数据操作更改 

- value------字段说明 
- name------重写属性名字 
- dataType------重写属性类型 
- required------是否必填 
- example------举例说明 
- hidden------隐藏
