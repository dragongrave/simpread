> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/515067)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

Centos 搭建 git 服务器
=================

1、安装 Git
--------

```
$ yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel


```

复制

```
$ yum install git


```

复制

创建一个 git 用户组和用户，用来运行 git 服务：

```
$ groupadd git


```

复制

```
$ adduser git -g git


```

复制

2、创建证书登录
--------

收集所有需要登录的用户的公钥  
公钥位于 id_rsa.pub 文件中，把我们的公钥导入到 / home/git/.ssh/authorized_keys 文件里，一行一个。

如果没有该文件创建它：

```
$ cd /home/git/
$ mkdir .ssh
$ chmod 700 .ssh
$ touch .ssh/authorized_keys
$ chmod 600 .ssh/authorized_keys


```

复制

3、初始化 Git 仓库
------------

首先我们选定一个目录作为 Git 仓库，假定是 / home/gitrepo/w3cschoolcc.git，在 / home/gitrepo 目录下输入命令：

```
$ cd /home
$ mkdir gitrepo
$ chown git:git gitrepo/
$ cd gitrepo


```

复制

**初始化一个仓库**

```
$ git init --bare w3cschoolcc.git 
//输出：Initialized empty Git repository in /home/gitrepo/w3cschoolcc.git/


```

复制

以上命令 Git 创建一个空仓库，服务器上的 Git 仓库通常都以. git 结尾。然后，把仓库所属用户改为 git：  
`$ chown -R git:git w3cschoolcc.git`

### 4、克隆仓库

```
$ git clone git@192.168.45.4:/home/gitrepo/w3cschoolcc.git
//Cloning into 'w3cschoolcc'...
//warning: You appear to have cloned an empty repository.
//Checking connectivity... done.


```

复制

192.168.45.4 为 Git 所在服务器 ip ，你需要将其修改为你自己的 Git 服务 ip。

这样我们的 Git 服务器安装就完成了  
接下来我们可以禁用 git 用户通过 shell 登录，可以通过编辑 / etc/passwd 文件完成。找到类似下面的一行：  
`git:x:503:503::/home/git:/bin/bash`  
改为：  
`git:x:503:503::/home/git:/sbin/nologin`