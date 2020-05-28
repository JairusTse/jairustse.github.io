---
layout: post
title: 使用virtualenv和virtualenvwrapper搭建python虚拟环境
category: python
tags: [python,virtualenv,virtualenvwrapper,虚拟环境]
---

`virtualenv`用于创建python虚拟环境，`virtualenvwrapper`用于管理虚拟环境。

# virtualenv

`virtualenv`可以为每个项目搭建独立的Python虚拟环境，与其他项目独立开来，保持环境的干净，解决包冲突问题。

下面的所有命令都是在MacOs的终端执行。

## 系统的Python环境
#### 查看系统Python版本
```
$ python3 --version
Python 3.7.0
```

#### 查看系统Python路径
```
$ which python3
/usr/local/bin/python3
```

#### 查看已安装库
```
$ pip3 list
Package            Version   
------------------ ----------
virtualenv         20.0.21 
```
可以查看所有安装了的库以及版本，这里只列出`virtualenv`。

## 安装
```
pip3 install virtualenv
```


## 使用
#### 创建虚拟环境`venv`
```
$ cd PycharmProjects
$ virtualenv venv
```
会生成虚拟环境`venv`目录，里面有独立的Python模块

#### 激活虚拟环境
```
$ source venv/bin/activate
(venv) xieyuedeMacBook-Pro:PycharmProjects xieyue$ 
```
有`(venv)`说明激活成功。

#### 默认虚拟环境
```
(venv) xieyuedeMacBook-Pro:bin xieyue$ ls
activate        easy_install-3.7    python3
activate.csh        easy_install3       python3.7
activate.fish       pip         wheel
activate.ps1        pip-3.7         wheel-3.7
activate.xsh        pip3            wheel3
activate_this.py    pip3.7
easy_install        python
```
虚拟环境的`pip`命令无效，不知道什么原因，网上貌似很少人遇到这个问题：
```
(venv) xieyuedeMacBook-Pro:bin xieyue$ pip
Traceback (most recent call last):
  File "/Users/xieyue/PycharmProjects/venv/bin/pip", line 6, in <module>
    from pip._internal.cli.main import main
ModuleNotFoundError: No module named 'pip._internal.cli.main'
```


#### 批量安装依赖库
```
$ pip3 freeze > requirements.txt
```
首先要到本地项目的根目录下执行导出依赖文件，会生成`requirements.txt`文件。

```
$ pip3 install -r requirements.txt
```
复制`requirements.txt`文件到虚拟环境，批量导入依赖库。

#### 退出虚拟环境
```
$ deactivate
```



# virtualenvwrapper
virtualenvwrapper是virtualenv的扩展工具，可以方便地创建、删除、复制、切换到不同的虚拟环境。


## 安装
```
$ sudo pip3 install virtualenvwrapper
```

#### 查看`virtualenvwrapper.sh`的路径
```
$ which virtualenvwrapper.sh
/usr/local/bin/virtualenvwrapper.sh
```

## 设置环境变量

#### 创建存放虚拟环境的目录：
```
$ mkdir ~/.virtualenvs
```

#### 打开`vim ~/.bashrc`并添加：
```
export WORKON_HOME=~/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3
source /usr/local/bin/virtualenvwrapper.sh
```

#### 使配置生效：
```
$ source ~/.bashrc
```

## 使用

#### 进入虚拟环境目录
```
$ echo $WORKON_HOME
/Users/xieyue/.virtualenvs
```

#### 常用命令
```
# 列出虚拟环境
$ lsvirtualenv -b 

# 创建虚拟环境
$ mkvirtualenv [虚拟环境名称]

# 切换虚拟环境
$ workon [虚拟环境名称] 

# 查看环境里安装了哪些包
$ lssitepackages 

# 进入当前环境的目录
$ cdvirtualenv [子目录名] 

# 复制虚拟环境
$ cpvirtualenv [source] [dest] 

# 退出虚拟环境
$ deactivate 

# 删除虚拟环境
$ rmvirtualenv [虚拟环境名称] 
```

