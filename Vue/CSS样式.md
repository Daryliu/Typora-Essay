1、z-index：数值越大则代表位置越靠上。

```css
/* 内容会被修剪，并且其余内容不可见 */
overflow: hidden;
```

2、vue内的js在引用动态图片时需要使用**require**，不然就只能将图片路径写死

3、[网页Request Headers请求头和Response Headers响应头](https://www.cnblogs.com/good7758/articles/5635981.html)

**Request Headers：**

​		Accept:告诉服务器，客户机支持的数据类型

​		Accept-Encoding:告诉服务器，客户机支持的数据压缩格式

​		Cache-Control：缓存控制，服务器通过控制浏览器要不要缓存数据

​		Connection:处理完这次请求，是断开连接还是保持连接

​		Cookie:客户机通过这个可以向服务器带数据

​		Host:访问的主机名

​		Upgrade-Insecure-Requests:参考http://www.cnblogs.com/hustskyking/p/upgrade-insecure-requests.html

​		User-Agent:告诉服务器，客户机的软件环境

**Response Headers响应头：**

　　Connection:处理完这次请求后，是断开连接还是继续保持连接

　　Content-Encoding:服务器通过这个头告诉浏览器数据的压缩格式

　　Content-Length:服务器通过这个头告诉浏览器回送数据的长度

　　Content-Type:服务器通过这个头告诉浏览器回送数据的类型

　　Date:当前时间值

　　Server:服务器通过这个头告诉浏览器服务器的类型

　　Vary:Accept-Encoding ——明确告知缓存服务器按照 Accept-Encoding 字段的内容，分别缓存不同的版本;参考：https://imququ.com/post/vary-header-in-http.html

　　X-Powered-By:服务器告知客户机网站是用何种语言或框架编写的。