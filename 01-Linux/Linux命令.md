# Linux操作命令

1. 公司内部124.71.29.202（gitlab服务器）可作为简易文件上传下载工具（<u>HTTPD（Apache</u>）），

   [参考链接]: https://blog.csdn.net/stalin_/article/details/90200173

   ```bash
   #linux下载redis，下载路径为当前目录
   wget http://download.redis.io/redis-stable.tar.gz
   ```

   将下载后的文件可作为当前服务器映射出去的下载文件，

   ```bash
   #下载124.71.29.202服务器中的文件
   wget http://124.71.29.202/download/smart3dmap/image-kong-2.6.tar；
   ```

   

