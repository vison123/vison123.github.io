---
layout: post
title:  Git使用
date:   2017-09-27 01:08:00 +0800
header-img: "img/in-post/git.jpg"
author:     "Vison"
catalog: true
tags:
    - Git
---

#### Git工作原理

如下图所示， git的工作总共分四层，其中三层是在自己本地也就是前面说的git仓库，包括了工作目录，暂存区和本地仓库，工作目录就是我们执行命令git init时所在的地方，也就是我们执行一切文件操作的地方，暂存区和本地仓库都是在.git目录，因为它们只是用来存数据的。远程仓库在中心服务器，也就是我们做好工作之后推送到远程仓库，或者从远程仓库更新下来最新代码到我们的git仓库。
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1506494614783&di=64d663b648da92cb34a9669fcdad8755&imgtype=0&src=http%3A%2F%2Fimages2015.cnblogs.com%2Fblog%2F492092%2F201704%2F492092-20170401154338789-1814759422.png)

#### Fork + MergeRequest使用

小型团队开发可以直接使用上图的工作模式，但是随着项目慢慢变大，团队成员变多这种工作模式的弊端就体现出来了。每次pull代码都有可能冲突，如果解决不好然后又直接推到远程仓库会造成后续问题。并且缺少代码审核的过程，对各成员的代码质量无法进行把控，这时候就应该使用Fork + MergeRequest模式进行开发。开发流程并没有大的变化，只不过你的远程仓库时fork出来的仓库，Fork repository中只有你一个人的代码提交（当然，随着团队的发展也有其他同事去fork你的仓库）。只有在提交到远程正式仓库的时候才会有新的变化：

* 如何保证同步代码来保证fork repository === origin repository；
* 同步正式库代码的时机应如何选择；

第一个问题，想要两个仓库的代码保持一致，只需直接pull origin repository的对应分支，解决冲突后push到fork repository即可；
第二个问题，同步正式库之前应该将本地所有改动commit后push到fork repository之后进行，这样就算本地仓库出现问题也不会丢失代码；

#### Git常用命令

* 查看、添加、提交、删除、找回，重置修改文件

```
git add <file> # 将工作文件修改提交到本地暂存区

git add . # 将所有修改过的工作文件提交暂存区

git rm <file> # 从版本库中删除文件

git rm <file> --cached # 从版本库中删除文件，但不删除文件

git revert <$id> # 恢复某次提交的状态，恢复动作本身也创建次提交对象

git revert HEAD # 恢复最后一次提交的状态
```

* 查看文件diff

```
git diff <file> # 比较当前文件和暂存区文件差异 git diff

git diff <id1><id1><id2> # 比较两次提交之间的差异

git diff <branch1>..<branch2> # 在两个分支之间比较

```
* 查看提交记录

```
git log <file> # 查看该文件每次提交记录

查看、切换、创建和删除分支

git br -r # 查看远程分支

git co <branch> # 切换到某个分支

git br -d <branch> # 删除某个分支

git br -D <branch> # 强制删除某个分支 (未被合并的分支被删除的时候需要强制)
```
* 分支合并和rebase

```
git merge <branch> # 将branch分支合并到当前分支
```

* Git暂存管理

```
git stash # 暂存

git stash list # 列所有stash

git stash apply # 恢复暂存的内容

git stash drop # 删除暂存区
```
* Git远程分支管理

```
git pull # 抓取远程仓库所有分支更新并合并到本地

git fetch origin # 抓取远程仓库更新

git merge origin/master # 将远程主分支合并到本地当前分支

git push # push所有分支

git push origin master # 将本地主分支推到远程主分支

git push -u origin master # 将本地主分支推到远程(如无远程主分支则创建，用于初始化远程仓库)

git push origin <local_branch> # 创建远程分支， origin是远程仓库名

git push origin <local_branch>:<remote_branch> # 创建远程分支

git push origin :<remote_branch> #先删除本地分支(git br -d <branch>)，然后再push删除远程分支

```

* Git远程仓库管理

```
git remote -v # 查看远程服务器地址和仓库名称

git remote show origin # 查看远程服务器仓库状态

git remote add origin http://github.com/vison123/vison123.github.io.git # 添加远程仓库地址

git remote set-url origin http://github.com/vison123/vison123.github.io.git # 设置远程仓库地址(用于修改远程仓库地址)

git remote rename <old> <new> # 修改远程仓库地址

git remote romove <repository> # 删除远程仓库

```
* Git submodule

```
git submodule add https://github.com/vison123/vison123.github.io.git framework  # 添加 submodule

git submodule update --init --recursive # 初始化 submodule 代码

```
