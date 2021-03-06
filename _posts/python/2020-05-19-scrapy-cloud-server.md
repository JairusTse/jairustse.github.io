---
layout: post
title: Scrapy爬虫部署到云服务器
category: python
tags: [python,scrapy,云服务器]
---

记录部署Scrapy爬虫项目到腾讯云服务器。

云服务器用的是腾讯云，没有特别的理由，用新人优惠买的，便宜。服务器配置是：CentOS 7.6 64位+1核+2GB+1Mbps。

主要有以下4个步骤：
#### 1、在云服务器搭建Python3环境
 
###### yum 源准备：
```
sudo yum -y update
sudo yum -y install yum-utils
sudo yum -y groupinstall development
```

###### 安装Python3：
```
sudo yum -y install epel-release
sudo yum -y install https://centos7.iuscommunity.org/ius-release.rpm
sudo yum -y install python36u
sudo yum -y install python36u-pip
```
###### 检查安装情况：
```
python3.6 -V
pip3.6 -V
```
###### 安装目录在：`\usr\bin\python3.6`

###### 添加软链接：
```
ln -s /usr/bin/python3.6 /usr/bin/python3
ln -s /usr/bin/pip3.6 /usr/bin/pip3
```

#### 2、创建项目虚拟环境：
###### 安装虚拟环境
安装：
```
sudo pip3 install virtualenv
python3 -m venv scrapy_venv
```
启用：
```
source /home/scrapy_venv/bin/activate
```

###### 安装Scrapy
```
pip3 install -i https://pypi.douban.com/simple/ scrapy
```

###### 安装MongoDB
创建仓库：
```
vi /etc/yum.repos.d/mongodb-org-4.2.repo
```
把下面的内容复制到文件中，保存退出：
```
[mongodb-org-4.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2013.03/mongodb-org/4.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
```
安装：
```
sudo yum install -y mongodb-org
```
修改配置文件：
```
vi /etc/mongod.conf
```
把bindIP改成 0.0.0.0，所有的机器都可以访问，保存退出。

启动Mongo服务：
```
service mongod start
```
验证Mongo服务：
```
netstat -nltp|grep mongo
```
关闭Mongo服务：
```
service mongod stop
```

#### 3、导入项目依赖文件到云服务器：
在本地导出依赖文件（本地项目目录下执行）：
```
pip freeze > requirements.txt
```
导入到云服务器（上传到云服务器项目目录后执行）：
```
pip3 install -r requirements.txt
```

列出当前虚拟环境所安装的依赖库
```
pip3 list
```

#### 4、运行项目
把本地Scrapy项目复制到云服务器虚拟环境的项目文件夹下，上传文件我用的是`FileZilla`。

执行爬虫：
```
cd /home/虚拟环境/项目文件夹/Scrapy项目

scrapy crawl 爬虫名称
或者
python 文件名.py
```
不过这样执行的话，ssh终端退出后，python进程也会被杀掉。需要创建一个自定义的系统服务，来保证python程序能够在后台运行。

创建系统服务：
```
vim /usr/lib/systemd/system/robot.service
```
复制下面内容到文件，保存退出：
```
[Unit]
Description=robot
After=network.target
 
[Service]
Type=forking
ExecStart=/home/spider/lagou/scrapy_venv/bin/python /home/spider/lagou/scrapy_venv/project/lagou/lagou/main.py

[Install]
WantedBy=multi-user.target
```

关于配置文件说明：  
__[Unit]区块__：服务描述、启动顺序与依赖关系   
`Description=robot`：服务描述  
`After=network.target`：网络启动后执行  

__[Service]区块__：不同的 unit type 就得要使用相对应的设置项目  
`Type=forking`：ExecStart字段将以fork()方式启动，此时父进程将会退出，子进程将成为主进程    
`ExecStart`：定义启动进程时执行的命令+参数  

__[Install]区块__：将这个unit安装到哪个target里面去  
`WantedBy=multi-user.target`：大多的服务性质的 unit 都是附挂在 multi-user.target 下面  

启用自定义系统服务：
```
systemctl enable robot
```
启动服务：
```
systemctl start robot
```
查看进程，确认服务是否启动：
```
ps aux|grep robot
```

完毕。



