---
title: 【每周一文】Git分支、标签管理与实战操作
date: 2017-03-28 22:57:29
tags: git
---

# 使用场景

**分支**

> 凌晨的中关村，灯火通明。

> 「领导，我 bot 项目的 A 模块进行到约 1/3，突然接到客户通知，B模块的优先级提升」，虽然是凌晨，小袁两眼依然炯炯有神。也许是第一次参加封闭开发，让小袁感觉很兴奋。

> 「好的，我给你新开一分支 B，在分支 B 上开发 B 模块，这样就不会影响你 A 模块的开发了」。

> 「Git 真是个神器」小袁带着对 Git 的崇拜，回到工位继续 Coding。

<!-- more --> 

**标签**

> 凌晨的中关村，灯火通明。

> 「领导，客户想看一下 bot 项目 v2.0版的代码，你那保存有吗？」，虽然是凌晨，小汪两眼依然炯炯有神。也许是第一次负责一个完整的产品开发，让小汪感觉很兴奋。

> 「好的，我给你下载一份，还好版本更新我打有 Tag 」。

> 「Git 真是个神器」小袁带着对 Git 的崇拜，回到工位继续 Coding。

# 如何使用

**分支**

```bash
# 查看本地分支
$ git branch

# 查看远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 创建本地分支
$ git branch [name] ----注意新分支创建后不会自动切换为当前分支

# 切换分支
$ git checkout [name]

# 创建新分支并立即切换到新分支
$ git checkout -b [name]

# 基于 master 分支创建 develop 分支
$ git checkout -b develop master

# 删除分支
$ git branch -d [name] ---- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项

# 合并分支
$ git merge [name] ----将名称为[name]的分支与当前分支合并

# 创建远程分支(本地分支push到远程)
$ git push origin [name]

# 删除远程分支
$ git push origin :heads/[name] 或 $ gitpush origin :[name] 


```

**标签**


```bash
# 列出已有的 Tag
$ git tag


# 创建轻量tag
$ git tag v1.0.1 

# 创建带附注的tag 建议用这个
$ git tag -a v1.0.1 -m "版本v1.0.1" 

# 查看tag信息
$ git show v1.0.1

# 只推送一个tag到远程
$ git push origin v1.0.1

# 一次性推送全部tag到远程
$ git push origin --tags

# 删除本地Tag
$ git tag -d v1.0.1


# 删除远程Tag
$ git push origin :refs/tags/v1.0.1

# 后期加注标签
$ git tag -a v1.0.1 9fceb02d0ae598e95dc970b74767f19372d61af8

# 切换到标签
$ git checkout v1.0.1

# 获取指定tag 的代码，并在此基础上编辑：先 git clone 整个仓库，然后 git checkout tag_name 就可以取得 tag 对应的代码了。但是这时候 git 可能会提示你当前处于一个“detached HEAD" 状态，因为 tag 相当于是一个快照，是不能更改它的代码的，如果要在 tag 代码的基础上做修改，你需要一个分支：

> $ git checkout -b branch_name tag_name

＃ 这样会从 tag 创建一个分支，然后就和普通的 git 操作一样了。

```

# 分支管理

1、master 分支，这个分支不干别的，只用来正式版本发布，只能有一个master分支，并且严格控制master分支的权限。

2、develop 分支，创建一个 develop 分支，大部分的主要开发工作都在此分支上进行。如果版本正式向外发布，那么需要在 master 分支上合并 develop 分支

```bash
# 切换到 master分支
$ git checkout master
# 对Develop分支进行合并
$ git merge --no-ff develop


```

--no-ff 参数是什么意思呢？指的是强行关闭 fast-forward 方式,默认情况下，Git 执行 `快进式合并`（fast-farward merge），会直接将 master 分支指向 develop分支，这种情况如果删除分支，则会丢失分支信息。因为在这个过程中没有创建commit。使用--no-ff参数后，会执行正常合并，在 master 分支上生成一个新节点，避免上述情况出现。

3、其它临时性的分支

比如你在开发过程中突然要改线上 Bug 这时可能要临时建一分支，这样的分支使用完以后，应该马上删除，确保代码库的的常用分支始终只有 master 和 develop。







