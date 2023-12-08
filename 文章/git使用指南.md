# git 使用指南.md

## 1. git 基本操作

> git init

    git init 初始化一个本地仓库

> git add .

    git add   将文件添加到暂存区
    例如 git add  Redis.md
    git add . 将当前目录下的所有文件添加到暂存区

> git commit -m "first commit"

    git commit 将改动提交到git本地仓库
> git remote add origin <server>

    为仓库添加一个远程服务器的地址
> git push origin master

    git push 将改动推送到远端仓库 可以将master换成任何分支

> git checkout develop

    你可以使用git checkout 创建或切换分支
    git checkout develop 创建一个develop分支并切换到该分支
    git checkout master  切换回主分支
    
> git branch
    
    你可以使用git branch 创建分支、删除分支、查看分支、重命名分支、创建本地跟踪分支并从远程分支拉取代码、建立当前分支与指定远程分支的追踪关系

## 使用场景
### 一、其中一个不想文件提交又不想放弃修改

    修改了4个文件，在不放弃任何修改的情况下，其中一个文件不想提交，如何操作？
    没add : git add 已经add: git reset --soft 
### 二、改到一半要恢复到未修改状态
     git checkout
### 三、改到一半被打断不想提交又要保存修改
    git stash
### 四、代码写一半，发现忘记切换分支了？
    git stash & git checkout
### 五、代码需要回滚了？
    git reset


> 未完待续......