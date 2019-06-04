---
title: 【Server】基于Node及MongoDB的服务配置与部署
date: 2019-04-19 10:52:36
tags:
    - Node
    - MongoDB
    - 服务部署
    - 日记
---
在过去几天的调研参考之后，拟定*Fork*Github相关的repositories，构建整站解决方案。
- 后端服务基于[node-elm](https://github.com/bayuefen/node-elm)
- 数据库采用[MongoDB](https://www.mongodb.com/)
- 商户系统Web服务基于[vue2-manage](https://github.com/bayuefen/vue2-manage)
- 用户系统Web服务基于[vue2-elm](https://github.com/bayuefen/vue2-elm)

本文主要根据Node及MongoDB，在服务配置与部署过程中所遇到的一些问题的解决方案进行总结。

<!-- more -->

### Node Server install
#### 安装依赖
1、node (6.0 及以上版本)
2、mongodb (开启状态)
3、GraphicsMagick (裁切图片)
4、yarn/npm(Node packages管理)
5、pm2(守护进程)

####  项目运行
````
git clone https://github.com/bayuefen/node-elm  

cd node-elm

yarn install / npm install

yarn run dev

````
访问: `http://localhost:8001`（如果已启动前台程序，则不需打开此地址）
本人将`node-elm`工程项目clone至阿里云ECS上，安装好上述环境依赖及node packages后，使用pm2守护进程
````
pm2 start 'yarn run dev'
````


###  MongoDB Community Edition install(.rpm Packages)
#### 安装流程
1、创建`/etc/yum.repos.d/mongodb-org-4.2.repo`文件;
2、写入`yum`安装配置信息
````
[mongodb-org-4.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.1/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
````
3、安装MongoDB packages
````
sudo yum install -y mongodb-org-unstable    
````
4、!特定版本packages及组件安装
````
sudo yum install -y mongodb-org-unstable-4.1.10 mongodb-org-unstable-server-4.1.10 mongodb-org-unstable-shell-4.1.10 mongodb-org-unstable-mongos-4.1.10 mongodb-org-unstable-tools-4.1.10
````

#### 启用流程

1、MongoDB默认文件创建
- /var/lib/mongo (the data directory)
- /var/log/mongodb (the log directory)

2、启用MongoDB
````
sudo service mongod start
````

3、验证启用是否成功
`27017`是默认端口，可以在`/etc/mongod.conf`中更改。
````
sudo chkconfig mongod on
````
4、关闭MongoDB
````
sudo service mongod stop
````
5、重启MongoDB
````
sudo service mongod restart
````
6、MongoDB使用
````
mongo
````

#### MongoDB权限配置
默认安装的MongoDB是不具备权限验证的，因为需要通过Navicat访问远程ECS上的DB服务，为了安全起见，给MongoDB配置相应的权限验证机制是非常必要的。
##### 超级管理员创建
````
# 启用mongoDB后使用
mongo

# 切换至超级管理员模式
use admin

# 创建超级管理员
db.createUser({user:'userName', pwd:'password', roles:['userAdminAnyDatabase']})

````
role类型:
- readAnyDatabase 任何数据库的只读权限(和read相似)
- readWriteAnyDatabase 任何数据库的读写权限(和readWrite相似)
- userAdminAnyDatabase 任何数据库用户的管理权限(和userAdmin相似)
- dbAdminAnyDatabase 任何数据库的管理权限(dbAdmin相似)

##### 创建用户
````
# 进入超级管理员模式并验证
use admin

db.auth('userName','password')

# 查看数据库
show dbs

# 进入到elm数据库
show elm

# 创建elm数据库用户
db.createUser({user:'user-bayuefen',pwd:'password',roles:[{role:'readWrite',db:'elm'}]})
````
以上步骤核心完成超级管理员的创建验证及单个数据库的用户创建。
具备了以上的权限配置，我们即可通过navicat进行数据库的查看与管理；同时，在`Node`服务中我们可以使用`mongoose` package对数据库进行CURD
###### Node
````
import mongoose from 'mongoose';
mongoose.connect('mongodb://user:pwd@localhost:27017/elm', {useMongoClient:true});
````
###### Navicat
````
server: ECS ip
port: 27017 (default)
user: user
pwd: password
db: db_name
````
ps:ECS需要通过阿里云后台服务打开安全策略中的27017端口的外网访问权限

#### 问题记录
Q:MongoDB在配置权限之后，重启Mongo,无法启动服务。抛`Unit mongod.service entered failed state.`异常
A:排查后，发现Centos需要将MongoDB添加至`systemd`中，否则会出现问题。[Issues](https://github.com/jingxinxin/tiankeng/issues/5)
添加`systemd`
````
vim /usr/lib/systemd/system/mongod.service

````
> [Unit]
> Description=mongodb database
>  
> [Service]
> User=mongod
> Group=mongod
> Environment="OPTIONS=--quiet -f /etc/mongod.conf"
> ExecStart=/usr/bin/mongod $OPTIONS run
> PIDFile=/var/run/mongodb/mongod.pid
> 
> [Install]
> WantedBy=multi-user.target

创建链接：
````
ln -s /usr/lib/systemd/system/mongod.service /etc/systemd/system/multi-user.target.wants/
````

重新加载systemctl
````
systemctl daemon-reload
````
#### 参考文档
- [Centos7](https://docs.mongodb.com/master/tutorial/install-mongodb-on-red-hat/#to-use-non-default-directories)
- [macOS](https://docs.mongodb.com/master/tutorial/install-mongodb-on-os-x/)
- [Windows](https://docs.mongodb.com/master/tutorial/install-mongodb-on-windows/)



