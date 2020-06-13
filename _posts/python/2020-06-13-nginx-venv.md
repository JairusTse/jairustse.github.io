---
layout: post
title: CentOS上安装LNMP和Nginx配置虚拟主机
category: python
tags: [python,CentOS,Nginx,LNMP,venv]
---

在CentOS上安装LNMP和Nginx配置多台虚拟主机。

## 概述

`Nginx`是开源、高性能、高可靠的Web和反向代理服务器，功能和Apache相似。

主要优点：
>①高并发响应性能好，能支持高达5w个并发连接数；  
②反向代理性能好（可用于负载均衡）；  
③内存和cpu占用率低（为Apache的1/5~1/10）。  

最常见的使用场景：
>①静态资源服务，通过本地文件系统提供服务；  
②反向代理服务，包括缓存、负载均衡等；  
③API服务，OpenResty。

我是使用Nginx + uWSGI搭建Python Web项目的服务器。

## 安装LNMP
__LNMP指的是Linux+Nginx+MySQL+PHP__  

这里可以只安装Nginx，之前想学PHP，所以一直安装的都是LAMP，现在改成LNMP，也可以安装LNMPA。

一键安装LNMP：[https://lnmp.org/install.html](https://lnmp.org/install.html)

安装完成后，查看安装目录：
```
cd /usr/local/nginx
```
Nginx基本结构如下：
![AltText](/assets/images/nginx_1.png)

常用命令：
```
# 启动
service nginx start 

# 停止
service nginx stop

# 重新启动
service nginx restart

# 重新加载
service nginx reload
```


修改配置文件：
```
vim /usr/local/nginx/conf/nginx.conf
```

最简单的配置文件：
```
user  www www;

worker_processes auto;

events
    {
        use epoll;
        worker_connections 51200;
        multi_accept off;
        accept_mutex off;
    }

http
    {
        include       mime.types;
        default_type  application/octet-stream;

        sendfile on;
        keepalive_timeout 60;

    server {
        listen       80;                  # 端口              
        server_name  location;            # 主机名  
        location / {
            root   html;                  # 项目根目录
            index  index.html index.htm;  # 起始页
        }
    }

include vhost/*.conf;
}

```

`server`节点下`listen`为监听的端口，默认是80；`server_name`是虚拟主机的名字；`location`是设置映射，默认映射到`/usr/local/nginx/html/`目录下的index.html，index.htm文件。

修改配置文件后需要执行命令重启Nginx服务器：
```
$ /usr/local/nginx/sbin/nginx -s reload
```


## 配置和添加虚拟主机
我们可以使用Nginx部署多个网站，每个网站部署在一台虚拟主机上。Nginx的配置文件中每一个server都代表一个虚拟主机，想要添加虚拟主机的话，在配置文件中添加一个server节点，并重新加载配置即可。

#### 1、添加server节点
```
user  www www;

worker_processes auto;

events
    {
        use epoll;
        worker_connections 51200;
        multi_accept off;
        accept_mutex off;
    }

http
    {
        include       mime.types;
        default_type  application/octet-stream;

        sendfile on;
        keepalive_timeout 60;

    server {
        listen       80;
        server_name  www.ipythondev.com;
        location / {
            root   /home/JairusTse/proxy-tool/;
            index  index.html index.htm;
        }
    }
    server {
        listen       80;
        server_name  www.lifetree.cn;
        location / {
            root   /usr/local/nginx/html/site1/;
            index  index.html index.htm;
        }
    }

include vhost/*.conf;
}

```

__配置完成后，记得重新加载配置：`/usr/local/nginx/sbin/nginx -s reload`__

如果直接这样通过域名访问，会出现“403 is forbidden“的报错，需要web目录的操作权限：
```
$ chmod -R 755 /home/JairusTse
```

配置成功的话，上面的配置可以通过`http://www.ipythondev.com`访问`/home/JairusTse/proxy-tool/`目录下的`index.html`文件，通过`http://www.lifetree.cn`访问`/usr/local/nginx/html/site1/`目录下的`index.html`文件。

#### 2、给每个虚拟主机添加配置文件

如果部署了几十上百个虚拟主机，为了方便管理，可以给每个虚拟主机添加配置文件。

创建`/usr/local/nginx/conf/vhosts`目录，创建与虚拟主机名一致的配置文件：
```
$ mkdir /usr/local/nginx/conf/vhosts;
$ cd /usr/local/nginx/conf/vhosts;
$ vim www.ipythondev.com;
```

配置文件内容：
```
#www.ipythondev.com
    server {
        listen       80;
        server_name  www.ipythondev.com;
        location / {
            root   /home/JairusTse/proxy-tool/;
            index  index.html index.htm;
        }
    }
```

修改Nginx配置文件：
```
user  www www;

worker_processes auto;

events
    {
        use epoll;
        worker_connections 51200;
        multi_accept off;
        accept_mutex off;
    }

http
    {
        include       mime.types;
        default_type  application/octet-stream;

        sendfile on;
        keepalive_timeout 60;

# 加载vhosts目录下所有配置
include vhost/*.conf;
}

```


同样，配置完后需要重启Nginx使配置生效。

完。




