看廖雪峰的官方网站有关Git的学习笔记

# 集中式分布式

**集中式**的版本控制系统：版本库集中的放在中央服务器，如果想在自己的电脑上干活就需要从中央服务器下载最新版本代码，干完活再提交。

缺点：必须联网才能工作，如果是互联网的话如果网速很慢，提交速度很慢。

**分布式**版本控制系统：每个人的电脑都有一个完整的版本库，这样就不需要上网了，如果A电脑修改了其中的A文件，B电脑也修改了同样修改了A文件，只需要把各自修改的部分推给对方。分布式版本控制系统只有一个充当“中央服务器”的设备，之为了方便大家修改。

# 在Windows上安装Git

# 版本库

版本库：仓库，repository，可以理解为一个目录，目录中的所有文件都能被Git管理，包括修改、删除，Git都能够跟踪以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

## 创建版本库

如何创建一个仓库：

1. 创建一个空文件
2. 进入这个空文件
3. `git init`

如何删除一个仓库：只需要删除其中的`.git`文件

## 把文件添加到版本库

编写一个`txt`文件`readme.txt`

```
Git is a version control system.
Git is free software.
```

1. 用命令`git add`告诉`Git`，把文件添加到仓库
2. 用命令`git commit`告诉`Git`，把文件提交到仓库

```bash
wjx@DESKTOP-DLQ1JA9 MINGW64 /d/study/JavaStudy/Git_Study
$ cd learngit/

wjx@DESKTOP-DLQ1JA9 MINGW64 /d/study/JavaStudy/Git_Study/learngit
$ git init
Initialized empty Git repository in D:/study/JavaStudy/Git_Study/learngit/.git/

wjx@DESKTOP-DLQ1JA9 MINGW64 /d/study/JavaStudy/Git_Study/learngit (master)
$ git add readme.txt

wjx@DESKTOP-DLQ1JA9 MINGW64 /d/study/JavaStudy/Git_Study/learngit (master)
$ git commit -m "wrote a readme file"
[master (root-commit) 90ec646] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```

`-m`后面输入的是本次提交的说明，最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

为什么Git添加文件需要`add`，`commit`一共两步呢？因为`commit`可以一次提交很多文件，所以你可以多次`add`不同的文件

# Git的基本操作

## 查看状态

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

`git status`命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，`readme.txt`被修改过了，但还没有准备提交的修改。

```bash
$ git diff
diff --git a/readme.txt b/readme.txt
index d8036c1..013b5bc 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
\ No newline at end of file
```

`git diff`顾名思义就是查看difference，显示的格式正是Unix通用的diff格式，可以从上面的命令输出看到，我们在第一行添加了一个`distributed`单词。

## 回退版本

假设现在已经有三个版本被提交到了代码仓库：

版本1：wrote a readme file

```
Git is a version control system.
Git is free software.
```

版本2：add distributed

```
Git is a distributed version control system.
Git is free software.
```

版本3：append GPL

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

`git log`命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是``append GPL``，上一次是`add distributed`，最早的一次是`wrote a readme file`

```
$ git log
commit eaebcae2d429f6fbe0892b82794d2a027e936f79 (HEAD -> master)
Author: Wang Jiaxin <524594973@qq.com>
Date:   Wed Oct 12 23:00:44 2022 +0800

    append GPL

commit 7c630a373d993d5c961d82ca9fcf19b351e8548a
Author: Wang Jiaxin <524594973@qq.com>
Date:   Wed Oct 12 22:57:15 2022 +0800

    add distri

commit 90ec646f1c0eb0fe04c3ce5be91f7618f18e6468
Author: Wang Jiaxin <524594973@qq.com>
Date:   Wed Oct 12 21:51:33 2022 +0800

    wrote a readme file


```

一大串类似`1094adb...`的是`commit id`（版本号）

首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`eaebca...`，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

现在，我们要把当前版本`append GPL`回退到上一个版本`add distributed`，就可以使用`git reset`命令：

```
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```

**如果现在使用git log就看不到之前已经回退的“append GBL”版本了，那要找到这个版本应该怎么办捏?**

可以找到此版本的commit id

```
$ git reset --hard eaebc
HEAD is now at 83b0afe append GPL
```

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向`append GPL`：

```ascii
┌────┐
│HEAD│
└────┘
   │
   └──> ○ append GPL
        │
        ○ add distributed
        │
        ○ wrote a readme file
```

改为指向`add distributed`：

```ascii
┌────┐
│HEAD│
└────┘
   │
   │    ○ append GPL
   │    │
   └──> ○ add distributed
        │
        ○ wrote a readme file
```

然后顺便把工作区的文件更新了。所以你让`HEAD`指向哪个版本号，你就把当前版本定位在哪。

**找不到commit id怎么办？**

Git提供了一个命令`git reflog`用来记录你的每一次命令：

```bash
$ git reflog
eaebcae (HEAD -> master) HEAD@{0}: reset: moving to eaebc
eaebcae (HEAD -> master) HEAD@{1}: reset: moving to eaebc
7c630a3 HEAD@{2}: reset: moving to head^
eaebcae (HEAD -> master) HEAD@{3}: commit: append GPL
7c630a3 HEAD@{4}: commit: add distri
90ec646 HEAD@{5}: commit (initial): wrote a readme file
```

## 工作区暂存区

### 工作区（Working Directory）

电脑中能看到的目录

<img src="C:\Users\wjx\AppData\Roaming\Typora\typora-user-images\image-20221012234638326.png" alt="image-20221012234638326" style="zoom:50%;" />

### 版本库（Repository）

![image-20221012234844463](C:\Users\wjx\AppData\Roaming\Typora\typora-user-images\image-20221012234844463.png)

stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

我们把文件往Git版本库里添加的时候，是分两步执行的：

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

修改`readme.txt`文件，再增加一个`license.txt`文件，执行`git status`

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        license.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

`readme.txt`被修改了，而`LICENSE`还从来没有被添加过，所以它的状态是`Untracked`

使用两次`git add`将两个文件分别加入缓存区之后，使用`git status`查看状态

```bash
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   license.txt
        modified:   readme.txt
```

现在，暂存区的状态就变成这样了：

![git-stage](https://www.liaoxuefeng.com/files/attachments/919020074026336/0)

所以，`git add`命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

```bash
$ git commit -m "understand how stage work"
[master a59191c] understand how stage work
 2 files changed, 3 insertions(+), 1 deletion(-)
 create mode 100644 license.txt
```

提交了之后工作区就干净了，可以用git status查看，而且版本库中的暂存区也没有东西。

```bash
$ git status
On branch master
nothing to commit, working tree clean
```

![git-stage-after-commit](https://www.liaoxuefeng.com/files/attachments/919020100829536/0)

git diff查看工作区和暂存区差异，

git diff --cached查看暂存区和仓库差异，

git diff HEAD 查看工作区和仓库的差异，

首先我们明确知道git diff是比较工作区和暂存区的文件的，如果此时暂存区为空，那么稍微有点不同，即：

1. 暂存区为空使用git diff：因为此时暂存区为空，此时使用git diff同样也是比较工作区和仓库，即和使用git diff HEAD结果相同
2. 暂存区不为空使用git diff：因为此时暂存区不为空，此时使用git diff比较的就是工作区和暂存区

git add的反向命令git checkout，撤销工作区修改，即把暂存区最新版本转移到工作区，

git commit的反向命令git reset HEAD，就是把仓库最新版本转移到暂存区。

有一个问题：

<u>比如我修改了readme的文件，把他add了，然后我再修改文件readme让readme的文件和版本库中的文件一致，然后我再调用git checkout删除缓存区的文件，为什么工作区的readme还需要commit？</u>



## 管理修改

Git跟踪并管理的是修改，而非文件。

什么是修改：新增了一行、删除了一行、更改某些字符、删了一些又加了一些。

第一次修改 -> `git add` -> 第二次修改 -> `git commit`

你看，我们前面讲了，Git管理的是修改，当你用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，`git commit`只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。

用`git diff HEAD -- readme.txt`命令可以查看工作区和版本库里面最新版本、

## 撤销修改



1. 如果在**工作区**不小心写错了，需要修改

```bash
git restore -- readme.txt
```

2. 如果把错误的文件已经提交到**缓存区**，要删除缓存区的文件

```bash
git restore -- staged readme.txt
```

1、2都会让这个文件回到最近一次`git commit`或`git add`时的状态。

3. 如果不小心已经提交到**版本库**

可以采用回退版本

4. 如果提交到远程仓库

寄

## 删除文件

如果删除了一个文件：`text.txt`

这样工作区和版本库中的文件就不一样了。

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    text.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

1. 如果是误删的，可以调用`git restore <file>`回复。
2. 如果像删除版本库中的文件，可以调用 `git rm <file>`删除版本库，并且`git commit`

# 远程仓库

## 添加远程库

我已经在本地建立了一个git库，又想在github上建立一个git仓库，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作，真是一举多得。

## 从远程库上克隆

我们根据GitHub的提示，在本地的`learngit`仓库下运行命令：

```bush
git remote add origin https://github.com/wjx951753/Git_Study.git
```

添加后，远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

```bash
git push -u origin main
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`main`推送到远程。

由于远程库是空的，我们第一次推送`main`分支时，加上了`-u`参数，Git不但会把本地的`main`分支内容推送的远程新的`main`分支，还会把本地的`main`分支和远程的`main`分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

```
$ git push origin main
```

把本地`master`分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库！

### 删除远程库

如果添加的时候地址写错了，或者就是想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息：

```
$ git remote -v
origin  git@github.com:michaelliao/learn-git.git (fetch)
origin  git@github.com:michaelliao/learn-git.git (push)
```

然后，根据名字删除，比如删除`origin`：

```
$ git remote rm origin
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

# 分支管理

## 创建与合并分支

## 解决冲突

## 分支管理策略



















