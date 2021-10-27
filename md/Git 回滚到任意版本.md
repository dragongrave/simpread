> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/602879)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

git 回滚到任意版本
===========

**回滚到指定的版本**

```
git reset --hard e377f60e28c8b84158


```

复制

**强制提交**

```
git push -f origin master


```

复制

完美  
ba87f5b62d581d3a109a78982ca478643c4c6800

先显示提交的 log

```
$ git log -3
commit 4dc08bb8996a6ee02f
Author: Mark <xxx@xx.com>
Date:   Wed Sep 7 08:08:53 2016 +0800

    xxxxx

commit 9cac9ba76574da2167
Author: xxx<xx@qq.com>
Date:   Tue Sep 6 22:18:59 2016 +0800

    improved the requst

commit e377f60e28c8b84158
Author: xxx<xxx@qq.com>
Date:   Tue Sep 6 14:42:44 2016 +0800

    changed the password from empty to max123


```

复制