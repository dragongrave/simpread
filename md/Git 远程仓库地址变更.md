> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/602877)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

Git 远程仓库地址变更
============

> 以下均以项目 git_test 为例：

```
老地址：http://192.168.1.12:9797/john/git_test.git 
新地址：http://192.168.100.235:9797/john/git_test.git 


```

复制

远程仓库名称： origin

方法一 通过命令直接修改远程地址
----------------

进入 git_test 根目录  
**git remote** 查看所有远程仓库， **git remote xxx** 查看指定远程仓库地址

```
git remote set-url origin http://192.168.100.235:9797/john/git_test.git


```

复制

方法二 通过命令先删除再添加远程仓库
------------------

进入 git_test 根目录

```
git remote 查看所有远程仓库， git remote xxx 查看指定远程仓库地址
git remote rm origin
git remote add origin http://192.168.100.235:9797/john/git_test.git


```

复制

方法三 直接修改配置文件
------------

进入 git_test/.git  
vim config

```
[core] 
repositoryformatversion = 0 
filemode = true 
logallrefupdates = true 
precomposeunicode = true 
[remote "origin"] 
url = http://192.168.100.235:9797/shimanqiang/assistant.git 
fetch = +refs/heads/*:refs/remotes/origin/* 
[branch "master"] 
remote = origin 
merge = refs/heads/master


```

复制

修改 [remote “origin”] 下面的 url 即可

方法四 通过第三方 git 客户端修改。
--------------------

以 SourceTree 为例，点击 仓库 -> 仓库配置 -> 远程仓库 即可管理此项目中配置的所有远程仓库， 而且这个界面最下方还可以点击编辑配置文件，同样可以完成方法三。