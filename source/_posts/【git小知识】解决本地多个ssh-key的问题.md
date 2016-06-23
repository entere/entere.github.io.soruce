---
title: 【git小知识】解决本地多个ssh-key的问题
date: 2016-04-01 23:35:24
tags: git
---

### 1.概述
在用`git`时，我们有这样的需求，要用`github`，还要用`gitlab`,或者`github`上多个不同的账号，这样，我们本机就需要生成不同的`id_ras`文件。github官方的说明文档要求备份当前的`id_rsa`，然后生成一份新的私钥用于`github`的登陆。如果真这样做，那么新的私钥是无法再继续登陆之前的机器的。这种方法有点暴力…
还好ssh可以让我们通过不同的私钥来登陆不同的域。
<!-- more --> 
### 2.首先，在新增私钥的时候，通过指定不同的文件名来生成不同的私钥文件


```
ssh-keygen -t rsa -f ~/.ssh/id_rsa.gitlab -C "**@blogchina.com"

ssh-keygen -t rsa -f ~/.ssh/id_rsa.github -C "entere@126.com"

```

### 3.然后，新增`ssh`的配置文件，并修改权限

```
touch ~/.ssh/config

chmod 600 ~/.ssh/config

```

修改`config`文件的内容：

```
Host github.com
    IdentityFile ~/.ssh/id_rsa.github
    User git
    
Host gitlab.blogcore.cn
    IdentityFile ~/.ssh/id_rsa.gitlab
    User git
    RSAAuthentication yes

```
这样在登陆的时候，ssh会根据登陆不同的域来读取相应的私钥文件