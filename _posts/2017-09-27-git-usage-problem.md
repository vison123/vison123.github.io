---
layout: post
title:  Git使用与Chrome调试分享
date:   2016-09-27 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Git
---

### Git的使用问题

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

```
查看、添加、提交、删除、找回，重置修改文件

git help <command> # 显示command的help

git show # 显示某次提交的内容 git show $id

git add <file> # 将工作文件修改提交到本地暂存区

git add . # 将所有修改过的工作文件提交暂存区

git rm <file> # 从版本库中删除文件

git rm <file> --cached # 从版本库中删除文件，但不删除文件

git reset <file> # 从暂存区恢复到工作文件

git reset -- . # 从暂存区恢复到工作文件

git reset --hard # 恢复最近一次提交过的状态，即放弃上次提交后的所有本次修改

git reset --soft # 恢复最近一次提交过的状态，保留提交后的修改

git revert <$id> # 恢复某次提交的状态，恢复动作本身也创建次提交对象

git revert HEAD # 恢复最后一次提交的状态

查看文件diff

git diff <file> # 比较当前文件和暂存区文件差异 git diff

git diff <id1><id1><id2> # 比较两次提交之间的差异

git diff <branch1>..<branch2> # 在两个分支之间比较

git diff --staged # 比较暂存区和版本库差异

git diff --cached # 比较暂存区和版本库差异

git diff --stat # 仅仅比较统计信息

查看提交记录

git log git log <file> # 查看该文件每次提交记录

git log -p <file> # 查看每次详细修改内容的diff

git log -p -2 # 查看最近两次详细修改内容的diff

git log --stat #查看提交统计信息

Git 本地分支管理

查看、切换、创建和删除分支

git br -r # 查看远程分支

git br <new_branch> # 创建新的分支

git br -v # 查看各个分支最后提交信息

git br --merged # 查看已经被合并到当前分支的分支

git br --no-merged # 查看尚未被合并到当前分支的分支

git co <branch> # 切换到某个分支

git co -b <new_branch> # 创建新的分支，并且切换过去

git co -b <new_branch> <branch> # 基于branch创建新的new_branch

git co $id # 把某次历史提交记录checkout出来，但无分支信息，切换到其他分支会自动删除

git co $id -b <new_branch> # 把某次历史提交记录checkout出来，创建成一个分支

git br -d <branch> # 删除某个分支

git br -D <branch> # 强制删除某个分支 (未被合并的分支被删除的时候需要强制)

 分支合并和rebase

git merge <branch> # 将branch分支合并到当前分支

git merge origin/master --no-ff # 不要Fast-Foward合并，这样可以生成merge提交

git rebase master <branch> # 将master rebase到branch，相当于： git co <branch> && git rebase master && git co master && git merge <branch>

 Git补丁管理(方便在多台机器上开发同步时用)

git diff > ../sync.patch # 生成补丁

git apply ../sync.patch # 打补丁

git apply --check ../sync.patch #测试补丁能否成功

 Git暂存管理

git stash # 暂存

git stash list # 列所有stash

git stash apply # 恢复暂存的内容

git stash drop # 删除暂存区

Git远程分支管理

git pull # 抓取远程仓库所有分支更新并合并到本地

git pull --no-ff # 抓取远程仓库所有分支更新并合并到本地，不要快进合并

git fetch origin # 抓取远程仓库更新

git merge origin/master # 将远程主分支合并到本地当前分支

git co --track origin/branch # 跟踪某个远程分支创建相应的本地分支

git co -b <local_branch> origin/<remote_branch> # 基于远程分支创建本地分支，功能同上

git push # push所有分支

git push origin master # 将本地主分支推到远程主分支

git push -u origin master # 将本地主分支推到远程(如无远程主分支则创建，用于初始化远程仓库)

git push origin <local_branch> # 创建远程分支， origin是远程仓库名

git push origin <local_branch>:<remote_branch> # 创建远程分支

git push origin :<remote_branch> #先删除本地分支(git br -d <branch>)，然后再push删除远程分支

Git远程仓库管理

git remote -v # 查看远程服务器地址和仓库名称

git remote show origin # 查看远程服务器仓库状态

git remote add origin git@ github:robbin/robbin_site.git # 添加远程仓库地址

git remote set-url origin git@ github.com:robbin/robbin_site.git # 设置远程仓库地址(用于修改远程仓库地址) 

git remote rm <repository> # 删除远程仓库

创建远程仓库

git clone --bare robbin_site robbin_site.git # 用带版本的项目创建纯版本仓库

scp -r my_project.git git@ git.csdn.net:~ # 将纯仓库上传到服务器上

mkdir robbin_site.git && cd robbin_site.git && git --bare init # 在服务器创建纯仓库

git remote add origin git@ github.com:robbin/robbin_site.git # 设置远程仓库地址

git push -u origin master # 客户端首次提交

git push -u origin develop # 首次将本地develop分支提交到远程develop分支，并且track

git remote set-head origin master # 设置远程仓库的HEAD指向master分支

也可以命令设置跟踪远程库和本地库

git branch --set-upstream master origin/master

git branch --set-upstream develop origin/develop

```

#### Webstorm Git使用

JetBrain出品的各种Idea已经成为非常流行的开发工具，也是我们现在正在使用的开发工具，所以掌握其git使用也是必须的。
其中界面工具

### Chrome调试

#### 调试界面各Tab页功能含义

1.Elements：查看网页布局，前段页面调试时可以在Elements页面进行动态修改添加元素，便于界面调试；

2.Console：打印日志，提供日志搜索，执行，根据Level过滤；

3.Sources：调试代码专用，提供断点调试，watch，调用栈等功能；

4.Performance标签页可以显示JS执行时间、页面元素渲染时间，不做过多介绍；

5.Network 网络请求标签页：可以看到所有的资源请求，包括网络请求，图片资源，html,css，js文件等请求，可以根据需求筛选请求项，一般多用于网络请求的查看和分析，分析后端接口是否正确传输，获取的数据是否准确，请求头，请求参数的查看；

6.Memory标签页可以查看CPU执行时间与内存占用，不做过多介绍；

7.Resources标签页会列出所有的资源，以及HTML5的Database和LocalStore等，你可以对存储的内容编辑和删除 不做过多介绍；

8.Security标签页 可以告诉你这个网站的安全性，查看有效的证书等；

9.Audits标签页 可以帮你分析页面性能，有助于优化前端页面，分析后得到的报告；


#### Js代码调试技巧
