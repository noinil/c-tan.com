+++
title = "在 CafeMol 项目上试用 git-svn"
description = "使用 git-svn 来 用 git 命令管理基于 svn 进行代码管理的 CafeMol."
date = "2016-09-15T20:25:00"
categories = ["technique"]
tags = ["git", "svn"]

slug = "git-svn-CafeMol"
summary = "CafeMol 是本组开发并使用的分子动力学软件， 目前基于 svn 进行代码管理。  对于年轻人来说， git 可能是比较熟悉的选择。  本文展示了使用 git 来参与开发的可能性。"
+++

`git-svn` - Bidirectional operation between a Subversion repository and Git.[^1]

## 写在前面的废话
---

很久以前当我准备开始学习版本控制的时候， 就已经有了 git 与 github， 因此我也少走
了许多弯路。  甚至在我写博士论文的时候， 全部都用 git 来进行 $\LaTeX$ 文件的管理。
这也是我惨淡的博士求学生涯中为数不多值得回味的快乐回忆。

后来我开始了博士后阶段的工作。  在新的组里遇到的代码管理工具居然是 svn。  没有办
法， 我自己只好想其他路子来克服从 git 切换到 svn 的焦虑。  我找到一个愚蠢但有效
的路径： 首先克隆 svn 项目， 然后 git 化， 并把 `.svn` 等目录加到 `.gitignore`
列表中， 之后就可以像普通 git 一样来管理代码了。  不过在给 svn 版本贡献代码时，
却需要对照 `git diff` 的结果， 一条条手动加入到 svn 代码库， 然后再进行危险的
`svn commit`。  有效但愚蠢。

事实上， git 本身就提供一个工具 `git svn` 来做我想做的事情： 周游于两种版本控制
体系之间。


<br />
## 概述
---

这里以我操作 CafeMol 的经过为例子， 写下一些简单的 `git svn` 流程。

<br />
### 从 svn 服务器获取拷贝

这是第一步：

```bash
git svn clone http://server.name.net/svn/cafemol/trunk cafemol --username tan
```

网上有许多教程提示说可以用 `-s` 选项来自动获取所有的 svn 分支。  很智能， 但是未
必实用。  以我们的 CafeMol 项目为例， 该项目有许多代码贡献者， 也于是有许多的
svn 分支， 每个分支都可能仅仅只是一些测试性的代码， 根本没有必要拷贝至本地。  此
外， `git svn` 过于智能到连已经删除的 svn 分支也会抓取。  这会导致 `git branch
-a` 变成又臭又长的一串红色列表。  至少爱清洁的人是不会这样做的。


<br />
### 从 svn 设置来创建 `gitignore`

很简单：

```bash
git svn show-ignore >> .git/info/exclude
```

<br />
### 简单的流程及命令

#### 从服务器更新

```bash
git svn rebase
```

#### 推送本地修改到服务器

```bash
git svn dcommit
```

#### 看 log

```bash
git svn log | less

# or:
git log
```

#### 创建本地分支

与基本的 `git` 命令使用方式一致：

```bash
git checkout -b develop
git add .
git commit -a
```

合并到 `master`:

```bash
git rebase master
git checkout master
git merge develop
```

这里使用 `rebase` 而不用 `merge` 是考虑保持 svn log 的简单线性。

<br />
### 注意

简单来说， 每一次 `git svn dcommit` 都会改写 `git commit` 的记录。  因此每次推送
之后， 不同分支与 master 之间就会出现奇怪的分叉行为： 因为本来的共同祖先的信息被
改写了。  因此在进行这些操作时需要格外注意： 最好在每次 dcommit 之后将本地的危险
分支给删除。

感觉还是很傻！

---

[^1]: https://git-scm.com/docs/git-svn
