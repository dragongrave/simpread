> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/622298)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

### Please move or remove them before you can merge

在使用 git pull 时, 经常会遇到报错:  
**Please move or remove them before you can merge**

这是因为本地有修改, 与云端别人提交的修改冲突, 又没有 **merge**.

如果确定使用云端的代码, 最方便的解决方法是删除本地修改, 可以使用以下命令:

```
git clean  -d  -fx ""
d  -----删除未被添加到git的路径中的文件
f  -----强制运行
x  -----删除忽略文件已经对git来说不识别的文件


```

复制

注意: 该命令会删除本地的修改, 最好先备份再使用

```
git clean 参数 
-n 显示 将要 删除的 文件 和 目录 
-f 删除 文件，-df 删除 文件 和 目录

git clean -n
git clean -df
git clean -f


```

复制