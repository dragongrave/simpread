> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/622295)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

Git pull 强制覆盖本地文件
=================

**远程获取最新版本到本地**

```
# git fetch --all  


```

复制

> 相当于是从远程获取最新版本到本地，不会自动 merge  
> 只是下载远程的库的内容，不做任何的合并

**彻底回退到某个版本，本地的源码也会变为上一个版本的内容，此命令 _慎用_ ！**

```
# git reset --hard origin/master 


```

复制

> 删除所有 git add 的文件（当然这不包括未置于版控制下的文件 untracked files）  
> 把 HEAD 指向刚刚下载的最新的版本