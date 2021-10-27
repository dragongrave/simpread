> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/liqingbo27/git/601500)

> Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

win10 生成 SSH keys
=================

SSH key 可以让你在你的电脑和 Code 服务器之间建立安全的加密连接。

先执行以下语句来判断是否已经存在本地公钥：

```
cat ~/.ssh/id_rsa.pub


```

复制

如果出现类似一下字符串，表示你还没创建 ssh key  
![](https://img.kancloud.cn/27/d1/27d1fdb3f56492b175c99ce92d2efddf_478x30.png)

如果你看到一长串以 ssh-rsa 或 ssh-dsa 开头的字符串, 你可以跳过 ssh-keygen 的步骤。

> 提示: 最好的情况是一个密码对应一个 ssh key，但是那不是必须的。你完全可以跳过创建密码这个步骤。请记住设置的密码并不能被修改或获取。

生成 ssh key
----------

在 git 窗口输入

```
ssh-keygen -t rsa -C "252588119@qq.com"


```

复制

然后一直回车  
这个指令会要求你提供一个位置和文件名去存放键值对和密码，你可以点击 Enter 键去使用默认值。  
如图：  
![](https://img.kancloud.cn/d7/40/d7401f236e2a30e2e2099571dc4c2ad0_562x323.png)

**查看生成的公钥**

```
cat ~/.ssh/id_rsa.pub


```

复制

![](https://img.kancloud.cn/fc/e0/fce0a9e536ae786dbba0ebb4eb2fda30_564x114.png)  
复制这个公钥放到你的个人设置中的 SSH/My SSH Keys 下  
请完整拷贝从 ssh - 开始直到你的用户名和主机名为止的内容。

到这里的时候，你的公钥已经创建成功了

扩展
--

通过下面方法可以拷贝你的公钥到你的粘贴板下  
请参考你的操作系统使用以下的命令：  
**Windows:**

```
clip < ~/.ssh/id_rsa.pub


```

复制

**Mac:**

```
pbcopy < ~/.ssh/id_rsa.pub


```

复制

**GNU/Linux (requires xclip):**

```
xclip -sel clip < ~/.ssh/id_rsa.pub


```

复制

Applications

Eclipse：  
如何在 Eclipse 中添加 ssh key: [https://wiki.eclipse.org/EGit/User_Guide#Eclipse_SSH_Configuration](https://wiki.eclipse.org/EGit/User_Guide#Eclipse_SSH_Configuration)  
Tip: Non-default OpenSSH key file names or locations

如果，不管你有什么理由，当你决定去用一个非默认的位置或文件名去存放你的 ssh key。你必须配置好你的 ssh 客户端以找到你的 ssh 私钥去连接 Code 服务器，对于 OpenSSH 客户端，这个通常是在~/.ssh/config 类似的位置配置的：

```
#
# Our company's internal GitLab server
#
Host my-git.company.com
RSAAuthentication yes
IdentityFile ~/my-ssh-key-directory/company-com-private-key-filename


```

复制