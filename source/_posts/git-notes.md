---
title: Git指南
categories: Tools
tags: git
abbrlink: 93ecec
date: 2019-07-12 16:11:50
---


[官网](https://git-for-windows.github.io/)

`git --version` 查看版本

git初始设置：
```bash
git config --global user.name = "name"
git config --global user.email = "email@example.com"
```

### 基础

git工作流程：
1. 工作区 
2. `git add` -> 版本库stage暂存区
3. `git commit` -> 版本库master分支（以及指向master的HEAD指针）
4. `git push` -> 远程仓库

`git config --global --list` 查看全局参数
<!-- more -->

### 创建版本库

`git init` 仓库初始化

添加文件至仓库版本库分两步：
```bash
git add --all   //将所有更改的文件一次性添加至暂存区
git add filename // 或指定添加单个file
git commit -m "message"  //将暂存区内容提交到当前分支，注明更改信息
```

`git status` 查看文件状态

`git diff` 查看文件修改前后差异
<!--more-->
 
### 版本管理（撤销、删除、回退）

`git log` 查看最近的提交日志

`git log --pretty=oneline` 单行显示提交日志

**撤销文件修改（尚未  `git commit`）分两种情况：**

1. 尚未使用 `git add` 命令添加到暂存区，直接撤销工作区中的修改：

`git checkout -- filename`

2. 部分代码已添加至暂存区后又做了修改，若希望全部撤销：

`git reset HEAD filename` 先回到暂存区的版本，之后再 `git checkout -- filename`

**删除文件**

本地文件手动删除，如果确认删除先 `git rm filename` 确认无误后 commit 

若是误删需恢复，则 `git checkout -- filename` 

**版本回退（已  `git commit` 但尚未 `git push`）**

`git reset --hard commitID` 利用 `git log` 得到的commitID返回版本

`git reflog` 查看历史版本及指针，内容太多翻不完按'q'退出


### GitHub远程仓库

生成ssh key：
```bash
ssh-keygen -t rsa -C "email@example.com"
```

登陆Github，填入id_rsa.pub里的内容后，测试是否可以连接上GitHub。
```bash
ssh -T git@github.com
```

创建远程仓库，然后本地关联：
```bash
git remote add origin git@github.com:user-name/repo-name.git   //关联远程repo
git push -u origin master   //本地首次推送，实际上是把当前分支master推送到远程
git push orgin master   //之后推送
```

将远程仓库clone至本地：
```bash
git clone git@github.com:user-name/repo-name.git
```

### 分支管理

HEAD指向当前分支，当前分支指向提交。

`git branch <name>` 创建分支

`git checkout <name>` 切换分支

`git checkout -b <name>` 创建+切换分支

`git branch -d <name>` 删除分支

`git branch -D <name>` 强行删除分支，用于无需合并直接删除的情况。

`git branch` 查看所有分支，当前分支前以 `*` 标注

`git merge --no-ff -m "merge with no-ff blalala" <name>`
普通模式（与fast-forward/recursive模式区分）：主要是为保留分支的相关信息，创建一个新的commitID，能看出曾经做过合并

`git log --graph --pretty=oneline --abbrev-commit` 查看分支合并图

git最佳实践，master为主分支，用来做正式发布版之后的保留历史，其他分支包括dev用来做正常开发，多个feature用来做某些特性功能，release用来做发布版历史，每次发布都是用release打包，hotfix用来做发布版之后的一些及时迭代修复bug的工作。

`git stash` 保存工作区现场，以便之后可恢复现场继续工作。用于处理紧急 bug，注意请先确保将开发分支的所有文件 git add 至暂存区，若直接切换分支会导致将开发分支的文件带入其他分支。

`git stash list` 查看保存的工作现场

`git stash pop` 恢复同时直接删除stash内容

`git stash apply stash@{num}` 用于当保存多份 stash 时，恢复指定工作现场。

### 多人协作

master、dev分支，要时刻与远程同步；bug分支只用于在本地修复bug，无需推送到远程；
feature新功能分支是否推送取决于你是否和其他人合作在上面开发。

`git remote -v` 查看远程库详细信息。

**多人协作通常工作模式**

从远程库clone，默认只看到本地的master分支，要在dev分支上操作需要创建远程origin的dev分支到本地：`git checkout -b dev origin/dev`，之后再本地dev上修改并push到远程即可。
考虑到项目发起人也会push内容：

1. `git push origin <name>` 试图推送自己的修改

2. 如果推送失败，说明远程分支与本地试图推送的内容有冲突，需要先用`git pull`试图抓取远程的新提交

3. 若`git pull`提示`There is no tracking information for the current branch.`则说明本地分支和远程分支的链接关系没有创建，解决方法：
`git branch --track origin/dev dev`

4. git pull 成功，解决冲突后，再次commit、push

### 标签管理

`git tag v1.0` 给当前分支打标签(自定义)，默认标签打在最新提交的commit上

`git tag` 查看所有标签，按字母排序。

补打遗漏版本标签：
```bash
git log --pretty=oneline --abbrev-commit
git tag v0.9 commitID
```

`git show v1.0` 查看标签信息。

`git tag -a v1.0 -m "version 1.0 released"` 创建带有说明的标签。

`git tag -s v1.0 -m "version <name> released"` PGP签名标签。

`git tag -d v1.0` 删除本地标签。

`git push origin v1.0` 推送指定标签到远程

`git push origin --tags` 推送标签到远程

若标签已推送到远程现需删除，分两步，首先本地删除，之后执行：
`git push origin :refs/tags/v0.9` 查看远程标签状态点击releases栏

### 自定义Git

`.gitignore` 编辑要忽略的文件，并提交到Git中，就可忽略特殊文件的检查。
如将 `*.db` 写入 `.gitignore` 文件中，将忽略所有db文件。

[github收集的各种.gitignore](https://github.com/github/gitignore)
<br/>

***参考资料***

[廖雪峰的Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)