> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/601501)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

在阿里云上搭建自己的 git 服务器
==================

这篇文章我就来介绍一下如何在一台全裸的阿里云主机上搭建自己的 git 服务器。

输入命令：

```
$ rpm -qa git 


```

复制

![](https://box.kancloud.cn/88a436629d97e50532b4c499058b35fa_415x37.png)

表示已经安装

1. 安装 git
---------

在 linux 系统中, git 安装只需要简单命令就可以完成, 只需要打开终端, 输入

```
$ yum install git # centos
$ apt-get install git # ubuntu


```

复制

2. 创建 git 用户及权限
---------------

建立用户组的目的在于对于这个 git 服务器，赋予多人访问权限时，可以统一管理。

```
$ groupadd git


```

复制

在用户组 git 下创建一个用户, 名字也为 git

```
$ adduser git -g git


```

复制

> 执行这条命令之后，你发现在 / home 目录下多了一个 git 目录，按理来说，现在，你的系统中多了这个 git 用户，并且家目录在 / home/git。但是，我们并不希望这个用户通过 ssh 连接到服务器上面去，所以，我们要禁止这个用户使用 ssh 连接上去进行操作。我们通过编辑一个权限文件来处理：

```
$ vi /etc/passwd


```

复制

找到类似于

```
git:x:1001:1001:,,,:/home/git:/bin/bash


```

复制

这样的行，你看到那个末尾的 / bin/bash，就是允许 ssh 连接操作的权限，我们把它改为 / user/bin/git-shell，结果如下：

```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell


```

复制

这样处理好，git 就不能 ssh 连上去了（实际上是可以的，只不过会闪退）。

我们还得给 git 分配一个密码，执行：

```
$ passwd git 123456(你的密码)


```

复制

这个密码用在你后面提交代码的时候使用。

3. 初始化一个 git 仓库
---------------

我习惯把这类东西丢到 / var 下去，所以，我们在 / var 下面创建一个 git 目录

```
$ cd /var
$ mkdir git
$ chown -R git:git git
$ chmod 777 git
$ cd git


```

复制

> chown -R git:git git , 第一个 git 是用户名，第二个 git 是组，第三 git 是文件夹名

接下来，我们用 git 命令初始化一个仓库：

```
$ git init --bare arepoforyourproject.git


```

复制

repoforyourproject 是仓库名，名字可以自己定义

初始化完成之后，这个空的仓库就 OK 了。

> 这里有一个细节，就是. git 目录必须要有可读写权限，因为当我们在 push 的时候，是使用 git 用户推送到服务器上面去，会有一个写入的过程，如果不赋予可写权限，push 就会失败。

> 记得修改 git 库的 `所有者` 把 root:root 改成 git:git 了

远程仓库的地址一般组成的格式是：

```
<用户名>@<服务器地址>:<仓库全路径>


```

复制

用户名就是当前登录的账号的名称，比如我当前用的是 git 账号，用户名就是 git  
服务器地址就是远程服务器的地址，比如 120.21.11.21  
仓库全路径这个也不难理解，直接在 project.git 目录下输入 pwd, 获取 project.git 的全路径。

4. 获取远程仓库地址
-----------

远程仓库的地址一般组成的格式是：

```
<用户名>@<服务器地址>:<仓库全路径>


```

复制

用户名就是当前登录的账号的名称，比如我当前用的是 git 账号，用户名就是 git  
服务器地址就是远程服务器的地址，比如 120.21.11.21  
仓库全路径这个也不难理解，直接在 project.git 目录下输入 pwd, 获取 project.git 的全路径。

比如：  
/home/git/gitRepository/pythonServer/project.git

那么整个远程仓库的地址就是：

```
git@120.21.11.21:/home/git/gitRepository/pythonServer/project.git


```

复制

5. 公钥
-----

这个是 git 里面比较特殊的一步操作，通信的时候，客户端与服务器需要一个证书进行验证。操作方法很简单，首先在你自己的电脑上（ubuntu）生成自己的一个公钥：

```
$ cd ~
$ ssh-keygen -t rsa


```

复制

这时你自己电脑上就有一个公钥了，但是在哪里呢？在. ssh 目录下，. 开头的文件夹都是隐藏的，但是可以 cd 进去。

```
$ cd .ssh
$ vi id_rsa.pub


```

复制

这样就能看到你的公钥了，把所有的内容复制下来。接下来，我们去回服务器上面操作。

```
$ cd /home/git/
$ mkdir .ssh
$ cd .ssh
$ vi authorized_keys


```

复制

如果是裸机，服务器上面 / home/git 目录下应该没有. ssh 目录，所以我们自己创建，打开（自动创建）authorized_keys 之后，把刚才复制下来的公钥黏贴进去，ok 了，保存退出。

使用证书，主要是为了无需密码就可以提交代码  
具体请看[《使用 SSH 证书远程登陆你的服务器》。](https://www.tangshuang.net/1697.html)

6. 多用户和权限管理
-----------

如果团队很小，把每个人的公钥收集起来放到服务器的 / home/git/.ssh/authorized_keys 文件里就是可行的。如果团队有几百号人，就没法这么玩了，这时，可以用 [Gitosis](https://github.com/res0nat0r/gitosis) 来管理公钥。

7. 服务端授权 ssh 公钥
---------------

```
# cd /home/git/
# mkdir .ssh
# chmod 700 .ssh
# touch .ssh/authorized_keys
# chmod 600 .ssh/authorized_keys
# chown -R git:git .ssh  


```

复制

其中`/home/git`目录为服务器上用户`git`的主页目录，上诉操作相当于在`/home/git/.ssh/`目录下新建一个`authorized_keys`文件。并把目录`.ssh`的权限设置为 700，`authorized_keys`文件权限设置为 600.  
因为 git 的 pull 相当于读操作，push 相当于写操作，所以需要读写权限。

接下来要做的是将客户端的公钥注册到服务端中, 打开服务端控制台，输入：

```
cat "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC9+VK1abwzJg+VjxGwpwKnsYU3eBEjXolQKUfKxEAMO9DREvdFrvIF5KhBE9HJTp7CEFcAfgP6xkJdxchQcEUyPyda9mIz6M4OOeuuLcxJcrqqJWTN0Jj78I/kDUZUJZF7bcV4q0CyeZG1fo5ckjxOmFaYkCGcq8vmeuFySFpco71UMkudzclrtGa53AvfmuOP1u96CEud78p2gYrPP5qr9ZYBNM+E290ddGMV61rnEiL7taAsXMGpuCQSbI0/zBJ3YXvN/lJSOVHFSeMFbKv7WDSJFSiBVHXjtcDa5RvzzWaFMBV8+SW4zluhijp6Dvb7pHBaLhLg/JvOixmR1/or OboBear" >> ~/.ssh/authorized_keys 


```

复制

这双引号里面一大串的就是你之前复制的公钥, 整句命令所做的事情就是将客户端的公钥添加到服务端的 ssh 授权表中。

客户端授权
-----

[win10 生成 SSH keys](601500)