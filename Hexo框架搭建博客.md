## 如何使用Hexo框架搭建属于你自己的博客?

Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 [Markdown](https://links.jianshu.com/go?to=http%3A%2F%2Fdaringfireball.net%2Fprojects%2Fmarkdown%2F)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
**优点！！！不需要服务器可以直接部署在你的GitHub上**

## 安装

### 1.安装Git

Windows: [git官网](https://links.jianshu.com/go?to=https%3A%2F%2Fgit-scm.com%2F) 下载后会有一个Git Bash命令行工具
Linux：

```csharp
sudo apt-get install git
```

### 2.安装Node.js

Hexo是基于Node.js编写，所以需要安装一下Node.js和里面的npm包管理工具
Windows：[Node.js官网](https://links.jianshu.com/go?to=https%3A%2F%2Fnodejs.org%2Fzh-cn%2F)选择LTS版本
Linux：

```csharp
sudo apt-get install nodejs
sudo apt-get install npm
```

安装完成后打开命令行检查一下是否安装成功

```undefined
node -v
npm -v
```

### 3.安装Hexo

安装完Git和Node.js就可以来安装Hexo了，可以先在桌面创建一个blog文件夹，然后在命令行中cd到该目录下，在命令行中键入命令

```undefined
npm install -g hexo-cli
```

老规矩`Hexo -v`看一下版本号
到此就安装完成了
接下来我们初始化一下我们的Hexo

```bash
hexo init blog
cd blog
npm install
```

**我们来看看项目结构**
node_modules: 依赖包
public：存放生成的页面
scaffolds：生成文章的一些模板
source：用来存放你的文章
themes：主题
_config.yml: 博客的配置文件
**运行服务，走你**

```undefined
hexo server
```

在浏览器输入localhost:4000就可以看到你生成的博客了
可以用ctrl+c将服务关掉

### 4.GitHub上创建个人仓库

首先你得有一个GitHub账号
如果没有的话的就注册一个吧[GitHub官网](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2F)
注册完成后点击New repository新建一个仓库
仓库名称必须为**你的用户名.github.io**
只有这样以将你博客部署到GitHub的时候才会被识别
假设我的用户名是**kumiko77**那我的仓库名就是**kumiko77.github.io**
点击create repository创建完成

### 5.生成SSH添加到GitHub

打开你的GitBash

```bash
ssh-keygen -t rsa -b 4096 -C "你的GitHub邮箱"
```

然后一路回车 ，此时他会告诉你SSH文件已经生成了
在GitHub的setting中，找到SSH keys的设置选项，点击New SSH key
把你的**id_rsa.pub**里面的信息复制进去
然后在GitBash中查看是否成功

```css
ssh -T git@github.com
```

### 6.将Hexo部署到GitHun上

到这一步就可以讲我们的博客和Hexo关联起来了
打开我们的配置文件**_config.yml**
翻到最下面将内容修改为

```go
deploy:
  type: git
  repo: https://github.com/用户名/用户名.github.io.git
  branch: master
```

然后我们先安装deploy-git ，也就是部署的命令,这样你才能用命令部署到GitHub

```undefined
npm install hexo-deployer-git --save
```

接着我们

```undefined
hexo clean
hexo generate
hexo deploy
```

可以使用缩写

```dart
hexo generate => hexo g
hexo deploy => hexo d
```

`hexo deploy`之后可能会要你输入GitHub的用户名和密码
成功之后就可以在**http://用户名[.github.io](https://links.jianshu.com/go?to=http%3A%2F%2F.github.io)**看到你的博客了