[TOC]

## Git 常用命令

### 一、新建代码仓库

#### 1.在当前目录新建一个Git代码库

```bash
$ git init
```

#### 2.新建一个目录，将其初始化为Git代码库

```bash
$ git init [project name]
```

#### 3.下载一个项目和它的完整代码历史

```bash
$ git clone [url]
```



### 二、配置

Git的配置文件为.gitconfig，它可以在用户主目录下(全局配置)，也可以在项目目录下(项目配置)。

#### 1.显示当前Git的配置

```bash
$ git config --list
```

#### 2.编辑Git配置文件

```bash
$ git config -e [--global]
```

#### 3.设置提交代码时的用户信息

```bash
$ git config [--global] user.name "[name]"
$ git config [--global] user.ecmail "[email address]"
```



### 三、增加/删除文件

#### 1.添加指定文件到暂存区

```bash
$ git add [file1] [file2] ...
```

#### 2.添加指定目录到暂存区，包括子目录

```bash
$ git add [dir]
```

#### 3.添加当前目录的所有文件到暂存区

```bash
$ git add .
```

#### 4.对同一个文件的多处变化，可以实现分支提交

```bash
$ git add -p
```

#### 5.删除工作区文件，并且将这次删除放入暂存区

```bash
$ git rm [file1] [file2]...
```

#### 6.停止追踪指定文件，但该文件会保留在工作区

```bash
$ git rm --cached [file]
```

#### 7.文件改名，并将这改名文件放入暂存区

```bash
$ git mv [file-original] [file-renamed]
```



### 四、提交代码

#### 1.提交暂存区到仓库区

```bash
$ git commit -m'[message]'
```

#### 2.提交暂存区指定文件到仓库区

```bash
$ git commit [file1] [file2] -m '[message]'
```

#### 3.提交工作区自上次commit之后的变化，直接到仓库区

```bash
$ git commit -a
```

#### 4.提交时显示所有diff信息

```bash
$ git commit -v
```

#### 5.使用一次新的commit，代替上一次的提交 。 如果代码没有任何变化，则用来改写上一次commit的提交信息

```bash
$ git commit --amend -m '[message]'
```

#### 6.重做上一次commit，并包括指定文件的新变化

```bash
$ git commit --amend [file1] [file2] ...
```



### 五、分支操作

#### 1.列出所有本地分支

```bash
$ git barnch
```

#### 2.列出所有远程分支

```bash
$ git branch -r
```

#### 3.列出所有本地分支和远程分支

```bash
$ git branch -a
```

#### 4.新建一个分支，停留在当前分支

```bash
$ git branch [branch-name]
```

#### 5.新建一个分支，并切换到该分支

```bash
$ git checkout -b [branch]
```

#### 6.新建一个分支，指向指定commit

```bash
$ git branch [branch] [commit]
```

#### 7.新建一个分支，与指定的远程分支建立追踪关系

```bash
$ git branch --track [branch] [remote-branch]
```

#### 8.切换到指定分支，并更新工作区

```bash
$ git checkout [branch-name]
```

#### 9.切换到上一个分支

```bash
$ git checkout -
```

#### 10.建立追踪关系，在现有分支与指定的远程分支之间

```bash
$ git branch --set-upstream [branch] [remote-branch]
```

#### 11.合并指定分支到当前分支

```bash
$ git merge [branch]
```

#### 12.选择一个commit，合并到当前分支

```bash
$ git cherry-pick [commit]
```

#### 13.删除分支

```bash
$ git branch -d [branch-name]
```

#### 14.删除远程分支

```bash
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```



### 六、标签

#### 1.列出所有tag

```bash
$ git tag
```

#### 2.新建一个tag在当前commit

```bash
$ git tag [tag]
```

#### 3.新建一个tag在指定的commit

```bash
$ git tag [tag] [comit]
```

#### 4.删除本地tag

```bash
$ git tag -d [tag]
```

#### 5.删除远程tag

```bash
$ git push origin : refs/tags/[tagname]
```

#### 6.查看tag信息

```bash
$ git show [tag]
```

#### 7.提交指定tag

```bash
$ git push [remote] [tag]
```

####8.提交所有tag

```bash
$ git push [remote] --tags
```

#### 9.新建一个分支，指向某个tag

```bash
$ git checkout -b [branch] [tag]
```



### 七、查看信息

#### 1.显示有变更的文件

```bash
$ git status
```

#### 2.显示当前分支的历史版本

```bash
$ git log
```

#### 3.显示commit历史，以及每次commit发生变更的文件

```bash
$ git log --stat
```

#### 4.搜索提交历史，根据关键词

```bash
$ git log -S [keyword]
```

#### 5.显示某个commit之后的所有变动，每个commit占据一行

```bash
$ git log [tag] HEAD --pretty=format:%s
```

#### 6.显示某个commit之后的所有变动，其'提交说明'必须符合搜索条件

```bash
$ git log [tag] HEAD --grep feature
```

#### 7.显示某个文件的版本历史，包括文件改名

```bash
$ git log --follow [file]
$ git whatchanged [file]
```

#### 8.显示指定文件相关的每一次diff

```bash
$ git log -p [file]
```

#### 9.显示过去5次提交

```bash
$ git log -5 --pretty --oneline
```

#### 10.显示所有提交过的用户，按提交次数排序

```bash
$ git git shortlog -sn
```

#### 11.显示指定文件是什么人在什么时间修改过

```bash
$ git blame [file]
```

#### 12.显示暂存区和工作区代码的差异

```bash
$ git diff
```

#### 13.显示暂存区和上一个commit的差异

```bash
$ git diff --cached [file]
```

#### 14.显示工作区和当前分支最新commit之间的差异

```bash
$ git diff HEAD
```

#### 15.显示两次提交之间的差异

```bash
$ git diff [first-branch]...[second-branch]
```

#### 16.显示今天你写了多少行代码

```bash
$ git diff --shortstat "@{0 day ago}"
```

#### 17.显示某次提交的内容变化

```bash
$ git show [commit]
```

#### 18.显示某次提交发生变化的文件

```bash
$ git show --bame-only [commit]
```

#### 19.显示某次提交时，某个文件的内容

```bash
$ git show [commit] :[filename]
```

#### 20.显示当前分支的最近几次提交

```bash
$ git reflog
```



### 八、远程同步

#### 1.更新远程仓库

```bash
$ git remote uopdate
```

#### 2.下载远程仓库的所有变动

```bash
$ git fetch [remote]
```

#### 3.显示所有远程仓库

```bash
$ git remote -v
```

#### 4.显示某个远程仓库的信息

```bash
$ git remote show [remote]
```

#### 5.增加一个新的远程仓库，并命名

```bash
$ git remote add [shortname] [url]
```

#### 6.拉去远程仓库的变化，并与本地分支合并

```bash
$ git pull [remote] [branch]
```

#### 7.推送本地分支到远程仓库

```bash
$ git push [remote] [branch]
```

#### 8.强行推送当前分支到远程仓库，即便有冲突

```bash
$ git push [remote] --force
```

#### 9.推送所有分支到远程仓库

```bash
$ git push [remote] --all
```



### 九、撤销

#### 1.恢复暂存区的指定文件到工作区

```bash
$ git checkout [flie]
```

#### 2.恢复某个commit的指定文件到暂存区和工作区

```bash
$ git checkout [commit] [file]
```

#### 3.恢复暂存区的所有文件到工作区

```bash
$ git checkout .
```

#### 4.重置暂存区的指定文件，与上一次commit保持一致，但工作区不变

```bash
$ git reset [flie]
```

#### 5.重置暂存区的指定文件，与上一次commit保持一致

```bash
$ git reset --hard
```

#### 6.重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变

```bash
$ git reset [commit]
```

#### 7.重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致

```bash
$ git reset --hard [commit]
```

#### 8.重置当前HEAD为指定commit，但保持暂存区和工作区不变

```bash
$ git reset --keep [commit]
```

#### 9.新建一个commit，用来撤销指定commit 后者的所有变化都将被前者抵消，并且应用到当前分支

```bash
$ git revert [commit]
```

#### 10.暂时将未提交的变化移除，稍后再移入

```bash
$ git stash
$ git stash pop
```















