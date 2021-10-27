> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/691727)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

git 丢弃本地修改的所有文件（新增、删除、修改）
=========================

本地修改了许多文件，其中有些是新增的，因为开发需要这些都不要了，想要丢弃掉，可以使用如下命令：

**本地所有修改的。没有的提交的，都返回到原来的状态**

```
git checkout . 


```

复制

**把所有没有提交的修改暂存到 stash 里面。可用 git stash pop 回复。**

```
git stash #


```

复制

**返回到某个节点，不保留修改。**

```
git reset --hard HASH


```

复制

**返回到某个节点。保留修改**

```
git reset --soft HASH


```

复制

**返回到某个节点**

```
git clean -df


```

复制

**git clean**

```
    -n 显示 将要 删除的 文件 和  目录
    -f 删除 文件
    -df 删除 文件 和 目录



```

复制

也可以使用：

```
git checkout . && git clean -xdf


```

复制