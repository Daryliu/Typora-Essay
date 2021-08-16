## cURL

curl 是常用的命令行工具，用来请求 Web 服务器。它的名字就是客户端（client）的 URL 工具的意思。

不带有任何参数时，curl 就是发出 GET 请求。

例：

```shell
curl www.baidu.com
相当于向www.baidu.com发出 GET 请求，服务器返回的内容会在命令行输出。
```

-X：-X参数指定 HTTP 请求的方法。

```shell
curl -X POST www.baidu.com
```

-H
-H参数添加 HTTP 请求的标头。

例：我们想发送json格式的数据时，就需要指定请求的Content-Type为 application/json，如下

```shell
 curl -d '{"name": "xxx", "pass": "123"}' -H 'Content-Type: application/json' https://www.baidu.com
```

-d：-d参数用于发送 POST 请求的数据体。

例：

```shell
curl  https://www.baidu.com/ -d 'name=xxx&password=xxx'
```

使用-d参数以后，HTTP 请求会自动加上标头Content-Type : application/x-www-form-urlencoded。并且会自动将请求转为 POST 方法，因此可以省略-X POST。

-o：-o参数将服务器的回应保存成文件，等同于wget命令，-o后为我们定义的文件名，即我们下载的文件保存的文件名

例：

```shell
curl -o baidu.html  https://www.baidu.com
```

-O：-O参数将服务器回应保存成文件，并将 URL 的最后部分当作文件名。

例：

```shell
curl -O https://www.baidu.com/baidu.html
```

