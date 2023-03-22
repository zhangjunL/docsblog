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

> 未完待续......