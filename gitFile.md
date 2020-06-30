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