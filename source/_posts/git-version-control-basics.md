title: Git版本控制基础应用
date: 2019-05-16 14:26:15
top: false
reward: false
comments: false
tags:
    - git
    - 日记
    - 版本控制
---

过去的一系列产品开发过程中，版本控制大多集中于svn的应用，对于git相对较为生疏。现阶段在开发一款企业级多系统应用过程中，对产品的版本分割、迭代有较强的前置需求，需要我自行维护构建一整套多版本的开发源码，因此借助公司内部已有gitlab平台做出基本尝试。
此文主要是用以记录采用git这一整套分布式版本控制系统过程中所归纳总结的一系列命令行、Repository、Branches等管理应用心得及踩坑之处。

<!-- more -->
#### 一、Git简介
分布式版本控制系统和集中式版本控制系统对于数据存储、安全性、文件流管理等各方面各有优异，以下仅对分布式版本控制系统做基本的优劣性总结。

> * git大部分系统以文件变更列表的方式存储信息，把存储数据看做对小型文件系统的一组快照流。
> * git绝大多数的操作只需要访问本地文件和资源，本次磁盘即保存着项目的完整历史，因此执行速度非常快。
> * git文件状态主要有：已提交(committed)、已修改(modified)、已暂存(staged)三种。对应的工作区域也有Git仓库、工作目录以及暂存区域三种。
> * git的工作流程：在工作目录中修改文件 -> 暂存文件，将文件的快照放入暂存区域 -> 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录
> * git存在较为明显的缺点，代码的保密性差，仓库保存着所有的代码和版本信息。


#### 二、Git安装及配置
基于Mac OS X的Git安装
###### 1.使用homebrew安装git ([homebrew](https://brew.sh/))
Homebrew安装
````
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
````
使用homebrew安装git
````
brew install git
````
###### 2.基于OSX Git安装程序安装（[https://git-scm.com/download/mac](https://git-scm.com/download/mac)）
###### 3.基于图形化工具GitHub for Mac工具安装（[https://desktop.github.com/](https://desktop.github.com/)）

#### 三、Git基本配置
检查配置信息
````
git config --list
````
用户信息配置
````
git config --global user.name "bayuefen"
git config --global user.email wobushifunv555@163.com
````

#### 四、命令使用

###### 常用命令
````
初始化操作：git init
克隆仓库：git clone [url] <name>
查看文件状态：git status
对比文件暂存：git diff
移除文件：git rm [file]
查看历史提交记录：git log
取消暂存文件：git reset HEAD <file>
查看远程仓库：git remote -v
冲突合并：git mergetool (可以使用opendiff等三方合并工具)
查看分支：git branch
创建分支：git branch <name>
切换分支：git checkout <name>
创建并切换到该分支：git checkout -b <name>
合并某分支到当前分支：git merge <name>
删除当前分支：git branch -d <name>
提交改动记录： git commit -m "changed records"
查看标签：git tag
添加标签：git tag -a [version] -m [version_name]
信息查看：git show
````
###### 拉取已有分支、开发并提交的基本流程
````
克隆现有repository：git clone domain/Project.git
切换至开发branch：git checkout <branch_name>
blingbling的苦逼工作：coding work...
分支整合：git pull
文件版本控制新增：git add ./
提交改动至分支：git commit -m <commit_record>
推送改动至分支：git push
````
###### 合并分支到master
````
切换至master分支：git checkout master
远程分支整合：git pull origin master
合并分支至master：git merge <branch>
查看状态：git status
推送分支内容至master：git push origin master
````

#### 五、References
1.[Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
2.[Pro Git](https://git-scm.com/book/zh/v2)









