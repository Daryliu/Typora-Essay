仓库存放项目代码，每个项目对应一个仓库
 Star收藏
 Fork克隆并添加分支

 每次重新进入建立的文件夹：
         1 cd d:进入D盘
         2 cd Git/进入Git文件夹
         3 cd testgit/进入testgit子文件夹


 git init 将目录变成Git可以管理的仓库
 git add readme.md用命令git add告诉Git，把文件添加到仓库/栈存区域
 git commit -m "。。。"用命令git commit告诉Git，把文件提交到仓库
 git log查询历史提交

 回到过去：
 git reset HEAD README.md将最近添加的文件恢复为未添加状态，即野生状态,不指定则为恢复所有。回滚到暂存区域
 git reset ---soft HEAD~撤销上一步操作，不会改变文件/git reset --hard HEAD~撤销可能还会改变文件
 回到指定快照：git reset 加ID值（版本快照）前几位字符即可
 回滚个别文件：git reset 版本快照 文件名/路径

 修改过文件后可选择新覆盖旧git /或者旧覆盖新git checkout -- README

---

#### 本机同时支持github和gitlab

**创建gitlab ssh**

```text
 ssh-keygen -t rsa -C "gitlab上的账户(我的是邮箱)" -f ~/.ssh/gitlab_id-rsa
```

**创建github ssh**

```text
 ssh-keygen -t rsa -C "github上的账户(我的是邮箱)" -f ~/.ssh/github_id-rsa
```

tips：注意生成两个平台得密钥文件名 要有区分 自己能看懂

**进入到ssh目录 创建config 文本 注意此文本没有后缀**

```text
cd ~/.ssh
touch config
```

**用编辑器打开 config文本，将此段代码复制进去，并修改**

```text
# github
Host github.com    
    Port 22
    User git
    HostName git.xxx.com,github.com #我这里填写了两个地址 亲测可以使用注意 英文逗号 正式环境删除此段注释
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa #这里填写上一步创建得密钥文件 注意要对应上 正式环境删除此段注释
# gitlab
Host gitlab.com
    Port 22
    User git
    HostName gitlab.com  
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/gitlab_id-rsa
```

tips: HostName # 井号后面得注释要去掉 我拉取代码得时候提示报错了 删除即可

**字段解释**

```text
Host
	它涵盖了下面一个段的配置，我们可以通过他来替代将要连接的服务器地址。这里可以使用任意字段或通配符。当ssh的时候如果服务器地址能匹配上这里Host指定的值，则Host下面指定的HostName将被作为最终的服务器地址使用，并且将使用该Host字段下面配置的所有自定义配置来覆盖默认的/etc/ssh/ssh_config配置信息。
Port
	自定义的端口。默认为22，可不配置
User
	自定义的用户名，默认为git，可不配置
HostName
	真正连接的服务器地址
PreferredAuthentications
	指定优先使用哪种方式验证，支持密码和秘钥验证方式
IdentityFile
	指定本次连接使用的密钥文件
```

**最后 将目录 ssh 中生成得密钥 添加到对应得平台（gitlab/github）**