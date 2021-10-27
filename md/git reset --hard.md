> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/622294)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

git reset -–hard
================

```
$ git reset --hard HEAD^         回退到上个版本
$ git reset --hard HEAD~3        回退到前3次提交之前，以此类推，回退到n次提交之前
$ git reset --hard commit_id     退到/进到 指定commit的sha码


```

复制

**git reset -–hard**  
彻底回退到某个版本，本地的源码也会变为上一个版本的内容，此命令 **慎用**！

**git fsck --lost-found**  
这个命令可以恢复 git add 过的文件

相关命令
----

*   git reset --mixed：此为默认方式，不带任何参数的 git reset，即时这种方式，它回退到某个版本，只保留源码，回退 commit 和 index 信息
*   git reset --soft：回退到某个版本，只回退了 commit 的信息，不会恢复到 index file 一级。如果还要提交，直接 commit 即可