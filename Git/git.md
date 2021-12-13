# Git的使用

### 1.1 使用前配置

在使用git前，需要告诉git你是谁，在向git仓库提交时需要用到

​	1.配置提交人姓名：`git config --global user.name 提交人姓名`

​	2.配置提交人邮箱：`git config --global user.email 提交人邮箱`

​	3.查看git配置信息：`git config --list`



### 1.2 提交步骤

-   `git init` 初始化git仓库
-   `git status` 查看文件状态
-   `git add 文件列表` 追踪文件，即将文件添加到暂存区中
-   `git commit -m 提交说明` 将暂存区中的文件提交到本地仓库
-   `git log` 查看提交记录



### 1.3 撤销

用暂存区中的文件覆盖工作目录中的文件：	`git checkout 文件`

将文件从暂存区中删除：	`git rm --cached 文件`

将git仓库中指定的更新记录恢复出来，并且覆盖暂存区和工作目录：	`git reset --hard commitID`



### 2.1 分支

为了便于理解，大家暂时可以认为分支就是当前工作目录中代码的一份副本

使用分支，可以让我们从主线上分离出来，以免影响开发主线



### 2.1.1 分支细分

1.主分支（master）：第一次向git仓库中提交更新记录时自动产生的一个分支。

2.开发分支（develop）：作为开发的分支，基于master分支创建。

3.功能分支（feature）：作为开发具体功能的分支，基于开发分支创建。



### 2.1.2 分支命令

-   `git branch` 查看分支
-   `git branch 分支名称`  创建分支
-   `git checkout 分支名称`  切换分支
-   `git merge 来源分支`  合并分支
-   `git branch -d 分支名称`  删除分支 （分支被合并后才允许删除）（-D强制删除）
-   ` git push --set-upstream origin 分支名` 添加远端分支（github）
-   `git push origin --delete 分支名` 删除远端分支（github）



### 3.1 本地仓库推送到远程仓库

   设置远程仓库别名

   `git remote add 自定义的仓库别名 远程仓库地址`

1.  `git push 远程仓库地址 分支名称`
2.  `git push 远程仓库别名 分支名称`
3.  `git push -u 远程仓库别名 分支名称`        -u 记住了推送地址别名，`下次推送只需要输入git push`即可



例子：

`git remote add origin http://github/yehong/test`

`git push -u origin master`



### 3.2 克隆仓库

`git clone 仓库地址`： 克隆远端数据仓库到本地



### 3.3 拉取远程仓库中最新的版本

`git pull 远程地址/远程地址别名 分支名称`



### 3.4 解决冲突

假设情景：

A修改了index.html文件的第二行，并提交到远程仓库

B在之后也修改了index.html文件的第二行，此时直接push到远程仓库是会失败的，

比较先 `git pull` 更新为最新版本，并查看文件冲突



如下为冲突位置

<<<<<<<< HEAD

A做出的修改

==========

B做出的修改

`>>>>>>>>>>>>>>> cf5151657451s2df1sad`



如A B做出的修改都需要保存则删除出土区域的符号改为如下

A做出的修改

B做出的修改



并保存提交推送到远程仓库即可



