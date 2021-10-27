> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/661711)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

码云上添加 SSH 公钥
============

一、打开终端输入
--------

```
ssh-keygen -t rsa -C "xxxxx@xxxxx.com"  
#Generating public/private rsa key pair...


```

复制

> 三次回车即可生成 ssh key  
> ![](https://img.kancloud.cn/1c/ba/1cba02ead57ce942e42814f37a8ae55f_555x335.png)

二、查看你的 public key
-----------------

```
cat ~/.ssh/id_rsa.pub
# ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC6eNtGpNGwstc....


```

复制

![](https://img.kancloud.cn/05/18/051821963ae206c461d68796c2645b6e_970x86.png)

三、码云上添加个人公钥
-----------

复制以上公钥，添加到马云的个人私钥里面

四、查看状态
------

**添加后，在终端（Terminal）中输入**

```
ssh -T git@gitee.com


```

复制

若返回以下信息，则证明添加成功。

```
Hi liqingbo! You've successfully authenticated, but GITEE.COM does not provide shell access.


```

复制

![](https://img.kancloud.cn/6d/22/6d226745f61a95eed8836bef85db0260_747x39.png)

五、配置 git pull/push 免密码
----------------------

进入项目根目录，也就是. git 所在目录  
终端输入

```
vi .git/config


```

复制

文件代码

```
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[remote "origin"]
        url = git@gitee.com:liqingbo/项目.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master


```

复制

修改配置文件

HTTPS 传输的地址，你需要改成 SSH 的传输地址

```
url = https://@gitee.com/liqingbo/项目.git


```

复制

改成

```
url = git@gitee.com:liqingbo/项目.git


```

复制

> 注：[https:// 改成 git 和 gitee.com](https://xn--gitgitee-1c2ne59hijh.com)，后面的 “/” 改成“:”

这样使用命令 git pull/push 就不用输入密码了，这是因为刚才在生成公钥时，没有输入密码，所以当你选择 SSH 地址传输时，就可免密码使用命令 git pull/push。