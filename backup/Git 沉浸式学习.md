
![git-logo](https://github.com/user-attachments/assets/86e730d1-5d74-448e-8018-056010740cae)

# 1.Git 简介

上大学时写论文，我的文件夹中躺着一堆文件：“溜溜球原理及应用.doc” 、“溜溜球原理及应用V2.doc”、“溜溜球原理及应用（最终版）.doc” 以及 “溜溜球原理及应用（最最最最终版）.doc” 等等，最后我都不记得哪个是哪个了……然后大神告诉我可以使用**版本控制**解决我的烦恼。

## 版本控制

版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。版本控制工具经过不断演进有很多类型：

![](./images/版本控制.png)

### 阶段一：本地版本控制系统

最早开始人们大多都是采用某种简单的本地数据库来记录文件的历次更新差异，例如 RCS。RCS 的工作原理是在硬盘上保存补丁集（补丁是指文件修订前后的变化）；通过应用所有的补丁，可以重新计算出各个版本的文件内容。

![本地版本控制图解](mages/local-version.png)

本地版本控制系统的缺点很明显，首先是如果本地磁盘出现问题，很容易丢失数据；其次，无法进行多人协作。

### 阶段二：中心化版本控制系统

为了解决上面的问题，人们设计了集中化的版本控制系统（Centralized Version Control Systems，简称 CVCS）。 这类系统，诸如 CVS、Subversion 以及 **SVN** 等，都有一个单一的集中管理的服务器，保存所有文件的修订版本，而协同工作的人们都通过客户端连到这台服务器，取出最新的文件或者提交更新。 多年以来，这已成为版本控制系统的标准做法。特别是 SVN 友好的图形化界面广受开发者青睐。

![集中化的版本控制图解](images/centralized-version.png)

但是中心化版本控制系统仍然存在单点故障导致数据丢失的问题。

### 阶段三：分布式版本控制系统

为了解决单点问题，人们又设计了分布式版本控制系统（Distributed Version Control System，简称 DVCS）。在这类系统中，像 Git、Mercurial、Bazaar 以及 Darcs 等，客户端并不只提取最新版本的文件快照， 而是把代码仓库完整地镜像下来，包括完整的历史记录。 这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。 因为每一次的克隆操作，实际上都是一次对代码仓库的完整备份。

![分布式版本控制图解](images/distributed-version.png)

分布式版本控制系统似乎没有什么缺点了，可能唯一缺点就是成本高。不过个人开发者有 Github 开源免费的代码仓库应该够用了，企业开发一般都要开发自己的代码仓库。

下面我们就开始介绍目前主流的版本控制工具：Git。

## Git 特点

Git 是 Linux 内核开源社区开发的一套版本管理系统，具备以下特点：

- 速度
- 简单的设计
- 对非线性开发模式的强力支持（允许成千上万个并行开发的分支）
- 完全分布式
- 有能力高效管理类似 Linux 内核一样的超大规模项目（速度和数据量）

### 直接记录快照，而非差异比较

Git 和其它版本控制系统的主要差别在于：其它大部分系统以文件变更列表的方式存储信息，即只存储一组基本文件和每个文件随时间逐步累积的差异 ，它们通常称作 **基于差异（delta-based）** 的版本控制；Git 是每次提交更新或保存项目状态时，对当时的全部文件创建一个快照并保存这个快照的索引， 为了效率，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。

![存储每个文件与初始版本的差异。](images/delta-snapshot.png)

### 近乎所有操作都是本地执行

在 Git 中的绝大多数操作都只需要访问本地文件和资源，一般不需要来自网络上其它计算机的信息， 因为你在本地磁盘上就有项目的完整历史，所以大部分操作看起来瞬间完成。

这也意味着你在离线或者没有 VPN 时，几乎可以进行任何操作。 如你在飞机或火车上想做些工作，就能愉快地提交（到你的 **本地** 副本，还记得吗？）， 直到有网络连接时再上传。

### Git 保证完整性

Git 中所有的数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情时更改任何文件内容或目录内容。 这个功能建构在 Git 底层，是构成 Git 哲学不可或缺的部分。 若你在传送过程中丢失信息或损坏文件，Git 就能发现。

Git 用以计算校验和的机制叫做 `SHA-1` 散列（hash，哈希）。 这是一个由 40 个十六进制字符（0-9 和 a-f）组成的字符串，基于 Git 中文件的内容或目录结构计算出来。 SHA-1 哈希看起来是这样：

```
24b9da6552252987aa493b52f8696cd6d3b00373
```

Git 中使用这种哈希值的情况很多，你将经常看到这种哈希值。 实际上，Git 数据库中保存的信息都是以文件内容的哈希值来索引，而不是文件名。

# 2.Git 入门

## 安装 Git

首先如果你的笔记本是 macOS  ，直接到这里下载 https://git-scm.com/download/mac ，就别 `Xcode Command Line Tools` 下载了，太慢了……另外推荐你们使用 [**iTerm2**](https://iterm2.com/) 终端，笔记本自带的 iTerm1 略丑。

![](images/image-20230412230743488.png)

如果你是 Dbian 系统，请使用命令行安装：

```sh
$ sudo apt install git-all
```

如果你是 centOS 系统，请使用 yum 包管理工具安装：

```sh
$ yum install git-all
```

如果你是 Windows 系统，推荐在 [GitHub for indows](https://desktop.github.com/) 下载。

**下载安装很简单，下载完去 [Github](https://github.com/mbadolato/iTerm2-Color-Schemes) 搜一个好看的主题，下载下来，按照上面的教程配置一下，你的 Git 终端就好看了。**

我们上面提到过 Git 是一个分布式系统，不同开发者的本地需要和远程服务器保持同步，那我们的 Git 本地自然需要记录远程服务器的访问信息了。远程在哪里？

自然是有一个开放的代码仓库 —— [GitHub](https://github.com/)。我们需要到这里注册一个自己的账号，这样你就拥有了一个自己私人的远程仓库。然后在本地设置好用户名密码，方便后续同步远程仓库的代码：

```shell
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

接下来你就可以玩转 Git 了！如果有什么不懂的直接通过 help 去查：

```sh
$ git help
```

或者查具体某个指令怎么用：

```sh
$ git help rebase
```

非常详细，比我还保姆……

到目前为止，我们的工具已经齐全了：

- GitHub 远程仓库的账号密码
- 本地的 Git 工具

下面就可以开始干活儿了。

## 新建项目

老板让你新建一个项目供大家共同维护，你这时候有两个选择：

1. 可以去 GitHub 新建项目；
2. 本地新建项目，然后推送到 GitHub。

### GitHub 新建项目

我们打开 GitHub 网页去新建项目：

![](images/image-20230412234346201.png)

这里你看到的 Owner 其实就是**命名空间**，以你自己的账号命名，也就是说这个项目创建好后是在你的命名空间下的。但是有个问题，如果这是一个多人合作的项目，放在你的个人空间下是不是就不合适了？

此时你就需要为你们这个小组建一个组织了：

![](images/image-20230412235712659.png)

建好后你就可以把小伙伴们都拉进来，基于该组织空间去新建一个项目：

![](images/image-20230412235841365.png)

你还可以添加一个 `README` 文件，建好后远程仓库就搞定了。

### 本地新建仓库 -- git init

我们也可以在本地新建仓库：

```sh
$ mkdir hello-world
$ cd hello-world
$ git init
```

这样我们就创建了一个名为 `hello-world` 的 Git 仓库，然后进行修改和提交，最后推送到 GitHub。

## 克隆项目 -- git clone

接下来你就需要把远程这个仓库下载到你本地，我们称为克隆：

![](images/image-20230413000331352.png)

此时你就用到了实战的第一个命令 `git clone` ：

```sh
$ git clone https://github.com/chatplayers/gaoshiqing.git
Cloning into 'gaoshiqing'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

你有没有注意到我截图 HTTPS 链接旁边还有两个选项：SSH 和 GitHub CLI。我们常用的是 HTTPS 和 SSH ，GitHub CLI 是 GitHub 的一个命令行工具，用的人不多，所以这种拉取代码的方式我们就不赘述了。

如果你想通过 SSH 的方式拉取，我们的本地需要通过 SSH 的方式访问远程仓库需要什么信息？了解过 Linux 的 [**ssh 命令** ](https://cloud.tencent.com/developer/article/1594881)的应该知道需要公钥和私钥。

所以需要你首先在本地生成一对公钥和私钥（注意指定好你要生成的密钥放在什么位置，不指定的话默认会生成到 `~/.ssh` 目录下）：

```sh
$ ssh-keygen -o
```

以 `id_rsa` 命名生成后私钥为 `id_rsa` 文件，公钥为 `id_rsa.pub` 文件：

![](images/image-20230413002230017.png)

你现在只需要把公钥中的内容保存到远程 `GitHub` 仓库的配置项中即可：

![](images/image-20230413002609206.png)

然后你就可以通过 ssh 方式拉取代码了：

```sh
$ git clone git@github.com:chatplayers/gaoshiqing.git
```

假如你还有一台笔记本，那也是一样的操作，在另一台笔记本上生成 ssh 密钥，然后上传公钥即可。

> **本地仓库 vs. 远程仓库**
>
> 我们从远程克隆代码到本地，本地这个项目我们就称之为**本地仓库**，我们在本地对文件进行任何更改，以及各种 git 操作，只要你不把这些变更**推送（`git pull` ）**到远程仓库，就不会对远程仓库造成任何影响。

## 查看文件状态 -- git status

拉取下来项目后，在项目下一通 coding ……快下班了你想把今天的成果提交到远程仓库。

首先，你要知道今天你改了哪些东西？要提交哪些东西？这时候就要使用 `git status` 查询一下你本地仓库中文件的状态：

```sh
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
    modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
    hello.yaml
```

可以看到描述展示了两部分内容：`Changes not staged for commit` 文件 `README.md` ，还有一个 文件 `Untracked` 文件  `hello.yaml`。Git 把文件的生命周期分为 4 个阶段：

- Untracked 新创建的文件
- Unmodified 没有变更的文件
- Modified 发生变更的文件（ Changes not staged for commit）
- Staged 暂存区的文件

> **什么是暂存区？**
>
> 你可以理解为是本地存储，你每次执行 `git add` 可以把你修改的文件添加到这里，然后提交代码（执行 `git commi`）时一次性把暂存区所有文件提交到本地仓库。

这四种状态流转如下图所示：

![](images/status.png)

1. 只有你编辑了文件，文件状态就会从 Unmodifed 变成 Modified
2. 执行 `git add` 命令可以将新创建的文件、变更的文件加入暂存区
3. 执行 `git reset` 命令可以将文件从暂存区剔除（你修改的内容不会丢失，只是从暂存区拿出来）
4. 执行 `git checkout` 命令可以擦除对文件的变更
5. 执行 `git rm` 命令可以删除一个文件，如果这次删除记录还没有提交到暂存区，文件状态会变为 Untracked
6. 执行 `git commit` 命令可以将暂存区的文件

## 对比文件差异 -- git diff

可是问题来了，我不确定修改的这个文件具体改了什么，应不应该提交，怎么办？使用 `git diff` 查看文件变更详情：

```sh
$ git diff README.md
diff --git a/README.md b/README.md
index 1789257..efdd44c 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,3 @@
 # gaoshiqing
 大家一起搞事情！
+new words
(END)
```

可以看到我新增了一行：new words ，+ 代表新增，- 代表删除。

## 撤销文件变更 -- git checkout

我对一个文件一通修改，后来发现改的不对，想恢复到改动前的状态，怎么办？使用 `git checkout` 命令擦除你上次提交之后到现在的所有修改：

```sh
$ git checkout README.md
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
    new file:   hello.yaml
```

可以看到之前对 `README.md` 文件的修改已经被擦除了。

## 添加文件到暂存区 -- git add

当你准备要提交代码了，我们上面说过，提交代码其实是把你放到暂存区的文件一次性提交到本地仓库。所以你需要先把要提交的代码通过 `git add` 添加到暂存区：

```sh
$ git add README.md
$ git add hello.yaml
```

然后我们再来看此时工作区的文件状态：

```sh
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
    modified:   README.md
    new file:   hello.yaml
```

可以看到这两个文件已经变成 `Staged`（`Changes to be committed`）了。加入我此时对 `hello.yaml` 又进行了修改，会是什么样子？

```sh
$ echo "hello: \"world\"" > hello.yaml
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
    modified:   README.md
    new file:   hello.yaml

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
    modified:   hello.yaml
```

可以看到有两个待提交的文件：

- README.md
- hello.yaml

还有一个发生变更的文件：

- hello.yaml

这是怎么回事呢？怎么会有两个 hello.yaml …… 其实，我们每次执行 `git add` 你可以理解为是保存当时的一次文件快照到暂存区。之后发生的变更是不会自动更新到暂存区的，如果你此时直接提交代码，那只会把暂存区的快照内容提交，而不会提交你后续对 `hello.yaml` 文件的变更。要想一起提交，你需要再次执行 `git add hello.yaml` 命令，这样会覆盖之前保存到暂存区的快照：

```sh
$ git add hello.yaml
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
    modified:   README.md
    new file:   hello.yaml
```

## 撤销暂存区的内容 -- git reset [file_name]

有个文件我加入到了暂存区，但是我突然想起有问题，暂时还不想提交，怎么办？—— 此时需要执行 `git reset` 命令。：

```sh
$ git reset README.md
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
    new file:   hello.yaml

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
    modified:   README.md
```

可以看到 `README.md` 文件已经从暂存区移除了，变成了已变更的状态。

`git reset` 命令除了可以撤销暂存区的内容，还可以撤销已提交的内容，我们稍后说。

## 提交暂存区文件到本地仓库 -- git commit

好了，现在工作结束了，你可以提交代码了。当然前提是你已经通过 `git add` 将需要提交的文件都加入到了暂存区，那你只需要执行 `git commit -m` 就可以把暂存区的所有内容都提交到本地仓库了：

```shell
$ git commit -m "add new file hello.yaml"
[main 7caf9b0] add new file hello.yaml
 1 file changed, 1 insertion(+)
 create mode 100644 hello.yaml
```

当你提交后发现，哎我还有一个文件忘记提交了，或者又改了一些代码，这些代码本应该和上一次提交合到一起才合理，你怎么办？再进行 `git add` 然后 `git commit -m "追加 xxx 文件"` ？可是这样提交日志里会记录很多次提交，不方便后面代码 `review` 。我们可以执行  `git add` 然后 `git commit --amend` 就可以和上一次提交合并到一起了：

注意，我们此时仅仅是将此次变更提交到了**本地仓库**，如果你这是去 GitHub 远程仓库查找，是看不到这次变更内容的。我们仔细看一下提交结果中有一个 `[main 7caf9b0]` ，这是干什么的？

前面的 `main` 代表当前所在**分支**，分支的概念我们后面讲；后面这个字符串是一个 Hash 字符串，唯一标识了这次提交，这其实就是指向这次提交的快照的指针 ，可以这样理解：

![提交对象及其父对象。](images/commits.png)

> **HEAD**
>
> HEAD 表示当前分支所在的提交节点位置，我们可以通过 `git reset` 调整 HEAD 指针的位置，后面在讲到 。

## 查看提交日志 -- git log

我们上面进行了提交，去哪里可以看到这次的操作日志呢？执行 `git log` 查看当前分支上的所有提交记录：

```sh
$ git log
commit 7caf9b01e0832235a108478f1392ec2c664c921b (HEAD -> main)
Date:   Fri Apr 14 14:07:08 2023 +0800

    add new file hello.yaml

commit f0074234a4358e1d00aba75fe51b8dffdbb5f8d1 (origin/main, origin/HEAD)
Date:   Thu Apr 13 00:01:47 2023 +0800

    Initial commit
```

可以看到最近一次提交的 HashCode 前几位和我们刚刚 `git commit` 的结果一致。

## 撤销提交 -- git reset [commit_id]

假如我发现刚刚提交的代码有些问题，需要撤销怎么办？

前面已经提过了，可以执行 `git reset commit_id` 的方式回退到指定的提交节点。 例如我要撤销刚刚的 `7caf9b0` 这次提交，那我回退到这次提交的前一个节点，也就是 `f0074234a` 这次提交：

```sh
$ git reset f0074234a
```

然后我们看一下文件状态：

```sh
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
    hello.yaml

nothing added to commit but untracked files present (use "git add" to track)
```

又恢复到了提交前的状态。然后我们看一下提交日志：

```sh
$ git log
commit f0074234a4358e1d00aba75fe51b8dffdbb5f8d1 (HEAD -> main, origin/main, origin/HEAD)
Date:   Thu Apr 13 00:01:47 2023 +0800
    Initial commit
```

可以看到刚刚的提交日志也被清理了。

> **tips：**我上面只想撤销一次提交，所以只需要回退到了上一个节点，假如你想撤销前面的连续几次提交也是一样的道理，你只要把 commit_id 指定为要回退到的节点就可以了。

## 推送文件到远程仓库 -- git push

我们把代码提交到本地仓库就完事儿了吗？没有，别忘记现在远程仓库还没有我们的代码呢！所以需要执行 `git push [仓库地址（可不填）] [分支名称（可不填）]` 命令将代码提交到远程仓库，如果后面的参数不填写，默认仓库为 `origin` ，分支为主干分支 `main`：

```sh
$ git push
```

如果你成功了，那你很幸运。你也可能会失败，别着急往下看……

## 管理远程仓库信息 -- git remote

如果你是第一次推送代码到远程仓库，可能直接推送成功了，也可能需要你输入 GitHub 账号密码，而且最后还失败了，例如：

```sh
$ git push
Username for 'https://github.com': imangozhang
Password for 'https://imangozhang@github.com':
fatal: Authentication failed for 'https://github.com/chatplayers/gaoshiqing.git/'
```

这是咋回事呢？这其实取决于我们的本地仓库和远程仓库是如何联系起来的……执行 `git remote` 命令可以管理这个关系。

首先，执行 `git remote -v` 可以查看远程仓库的地址：

```sh
$ git remote -v
origin  https://github.com/chatplayers/gaoshiqing.git (fetch)
origin  https://github.com/chatplayers/gaoshiqing.git (push)
```

这个地址是不是很眼熟？还记得第一步我克隆项目时的命令吗？

```sh
$ git clone https://github.com/chatplayers/gaoshiqing.git
```

没错，这里记录的就是当时我们克隆时的远程仓库地址。那我这里为什么失败了呢？上面的提示：**基于密码的鉴权方式已经在 2021.8.13 之后就禁用了，详情查看 https://docs.github.com/en... **。我们打开这个网址会看到：

<img src="/Users/imangozhang/Library/Application Support/typora-user-images/image-20230414200118488.png" alt="image-20230414200118488" style="zoom:70%;" />

哦，也就是说如果你维护的远程仓库地址是 https 格式的，要想执行 `git push`  等命令操作远程仓库，只能用 `access token` 的方式了，然后你需要根据这个链接的教程去创建一个 `access token` ，后续操作远程仓库时输入用户名和密钥即可。

但是这样其实很麻烦，用 ssh 的方式更简单一些，我们第一步在克隆项目时如何配置已经详细讲过了，你就按照上面的去配置一下公钥，然后执行 `git removte set-url `修改一下远程分支的地址就可以了：

```sh
$ git remote set-url origin git@github.com:chatplayers/gaoshiqing.git
```

然后重新提交代码就成功了：

```sh
$ git push
Enumerating objects: 4, done.
To github.com:chatplayers/gaoshiqing.git
   f007423..7666bc2  main -> main
```

## 同步远程仓库 -- git pull

我们提交了代码后，其他小伙伴想在我们的基础上去继续修改怎么办呢？他们当初也克隆了这个项目，可是他们的本地仓库并没有我们的提交内容啊……他们只需要执行 `git pull`  就可以把远程仓库的最新提交都同步到本地了：

```sh
$ git pull
remote: Enumerating objects: 5, done.
From github.com:chatplayers/gaoshiqing
   7666bc2..3cfcc68  main       -> origin/main
Updating 7666bc2..3cfcc68
Fast-forward
 hello.yaml | 4 ++++
 1 file changed, 4 insertions(+)
```

这里会告诉我们，相比于我们的本地仓库，同步远程仓库后发生了哪些变更。

> **`git pull` vs. `git fetch`**
>
> 同步远程仓库的命令除了 `git pull` 还有一个经常用的 `git fetch`:**`git pull` = `git fetch` + `git merge`**
>
> - `git fetch` 只是单纯把远程仓库的某个分支同步到本地仓库，如果和本地仓库进度不一致，需要手动执行 `git merge`
> - `git pull` 同步远程仓库后，自动合并。

## 忽略文件变更 -- .gitignore

有时候我们的项目里有些文件会随着我们的修改不断变更的，但是这些文件的变更对我们项目而言没什么用，所以不需要每次都提交，那这时候我们最好可以忽略这些文件的变更。在这种情况下，我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式，例如：

```sh
$ cat .gitignore
*.DS_Store
```

这是一个正则表达式，表示忽略该项目中所有以 `.DS_Store` 结尾的文件变更。

# 3.Git 进阶

当你完成了上面的练习，是不是觉得自己已经可以玩转 Git 了？那你就太年轻了……

我们前面说过，你这个项目可能是好几个人甚至成百上千人共同维护，你有没有想过这些问题：

- 你刚刚写好推送到远程仓库，然后别别人同步后一通乱改然后提交了，你怎么办？
- 你和别人都在修改同一个文件的同一个地方，你俩都往远程分支推送代码，此时以谁的为准？
- 假如你准备基于现在最新的代码部署到生产环境，可是恰巧有人又提交上来一些代码，还是错的，这怎么办？

……

类似问题还有很多，几乎所有的版本控制系统都是通过**分支**来解决这些问题的，Git 也一样。

## 分支简介

我们上面讲解 `git commit` 的时候，讲到 `git commit` 命令执行完后返回了  `[main 7caf9b0]`  内容，前面就是分支名称，后面是指向此次提交后的指针。我们现在只有一条主干链路，就是 `main` ，我们把这条链路称为**主干分支**。这个 `main` 你可以看作指向主干分支最新提交的头节点。

如果你想基于现在最新提交自己一个人搞事情，不想受其他人代码提交的干扰，怎么办呢？那就要创建一个分支了……

## 分支管理

### 新建分支 -- git checkout -b

假如你要独立开发一个 [oauth](https://oauth.net/2/) 的需求，可以通过 `git checkout -b [新分支名称] [源分支名称（可不填）]` 创建一个分支。源分支填和不填有什么区别呢？

- 不填源分支默认基于本地仓库当前分支切新分支
- 填源分支可以指定基于远程仓库或者本地仓库的某个分支切新分支

例如我们想基于现在最新的主干分支 `main` 来切一个新分支 `feature/oauth` ，有两种方式。

- 方式一

我们先把本地仓库同步到最新，然后基于本地仓库的 `main` 分支来切新分支：

```sh
$ git pull
From https://github.com/chatplayers/gaoshiqing
   f007423..3cfcc68  main       -> origin/main
Updating f007423..3cfcc68
Fast-forward
$ git checkout -b feature/oauth
branch 'feature/oauth' set up to track 'origin/main'.
Switched to a new branch 'feature/oauth'
```

- 方式二

我们直接基于远程仓库最新的 `main` 分支来切新分支：	

```sh
$ git checkout -b feature/oauth origin/main
branch 'feature/oauth' set up to track 'origin/main'.
Switched to a new branch 'feature/oauth'
```

创建好分支后，我们的流程图就更新为：

![](images/git-new-branch.png)

可以看到当前的 `feature/oauth` 分支和 `main` 分支都指向了同一个提交节点，后续随着你不断的在该分支上提交内容，`feature/oauth` 会向后移动：

![](images/git-branch-commit.png)

### 查询分支 -- git branch

我们刚刚新建了分支，可以通过 `git branch` 命令查看仓库中的所有分支（包括远程和本地）：

```sh
$ git branch
* feature/oauth
  main
(END)
```

你也可以执行 `git branch -v` 命令查看各分支的最后一次提交：

```sh
$ git branch -v
* feature/oauth 3643f28 [ahead 2] add name for hello.yaml
  main          3cfcc68 Update hello.yaml
(END)
```

你注意到 * 了吗？* 表示当前操作所在的分支，我们刚刚不是在 `master` 分支么，怎么就到了`feature/oauth` 分支了？其实是我们一开始执行 `git checkout -b` 时自动就帮我们切换过来了，回去看一下新建分支是命令执行结果最后一行：

```sh
Switched to a new branch 'feature/oauth'
```

### 切换分支 -- git checkout [branch_name]

如果我们想切换到其它分支做一些其他的修改怎么操作？执行 `git checkout [分支名称]` 命令：

```sh
$ git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
```

### 删除分支 -- git branch -d/D

假如我们刚刚那个分支不想要了，想删除掉它，执行 `git branch -d` 命令：

```sh
$ git branch -d feature/oauth
error: The branch 'feature/oauth' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature/oauth'.
```

此时发现删除失败了，原因是该分支还没有合并到我们的主干分支 `main` ，如果你此时删除的话该分支上的所有提交都会丢失，GitHub 对此进行了提示。

所有此时要么把 `feature/oauth` 分支先合并到 `main` ，要么执行 `git branch -D`  强制删除：

```sh
$ git branch -D feature/oauth
Deleted branch feature/oauth (was 3643f28).
```

这样就删除成功了。

> **tips：**不能直接删除当前所在分支，需要切换到其它分支后才可以删除。

### 提交合并请求 -- pull request

我们在自己的分支上开发完成了，最终要合并到主干分支，方便统一维护。执行 `git merge [仓库地址（可不填）]:[分支名称（可不填）]` 可以把当前分支合并到指定分支。

*我们可以先在自己的本地仓库把 `hotfix/authors`  分支合到本地仓库的 `main` 分支，然后推送到远程仓库。但是这样做并不规范，一般情况我们写完代码提交后，一般要让同组的其他伙伴进行 `Code Review` （重新检查一遍代码）之后，确定没问题了才能合到主干分支，避免污染主干分支。*

首先，我们要把自己的个人分支 hotfix/authors 推送到远程仓库：

```sh
$ git push origin hotfix/authors
Enumerating objects: 8, done.
To github.com:chatplayers/gaoshiqing.git
 * [new branch]      HEAD -> feature/oauth
```

推送成功后我们打开 GitHub 远程仓库，创建一个 `Pull Request` 提交合并请求 ：

![](images/image-20230415150225143.png)

`Pull Request` 简称 `PR`，可以通俗理解为合并请求，创建是指定哪些人可以通过这个请求并合并代码，我们一般称这个过程为 Code Review：

![](images/image-20230415150853398.png)

提交后，在 `Code Review` 人员列表内的伙伴查看完你的代码，认为没有问题了，就可以通过后把代码合入主干分支。

> **重点：**我们提交 `Pull Request` 合入到 `master` 的过程，相当于在远程仓库执行了 `git merge master` 命令，只是中间通过 `Pull Request` 的交互绕了一道，便于 `Code Review` 。

## 分支合并

### 合并算法

上面的 `pull request` 背后其实是执行了 `git merge` 命令，在执行 `git merge` 时两个分支结构不同对应的合并算法也不同，共有三种合并算法：

- Fast-Forward
- Three-Way
- Squash

#### 算法一：Fast-Forward

如果合并的两个分没有分叉，例如我们将一个 bug 修复的分支合 `hotfix/author` 并到 `main` 分支。流程图示如下：

![](images/git-origin-branch-1.png)

我们也可以通过 `git log --oneline --graph --decorate --all` 命令查看提交历史：

```sh
$ git log --oneline --graph --decorate --all
* f6b44a1 (hotfix/authors) c4
* 8121a15 c3
* 2a8def6 (HEAD -> main) c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

这时候如果我们执行 `git merge` 把 `hotfix/authors`  分支合入 `main` 分支：

```sh
$ git merge hotfix/authors
Updating 2a8def6..f6b44a1
Fast-forward
 2 files changed, 2 insertions(+)
 create mode 100644 authors/relation.java
```

可以看到合并算法默认选择 `Fast-Forward` ，最终效果：

```sh
$ git log --oneline --graph --decorate --all
* f6b44a1 (HEAD -> main, hotfix/authors) c4
* 8121a15 c3
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

其实就是直接把 `main head` 指针移动到最近一次提交 `hotfix/authors head` 处。可以用流程图描述为：

![](images/git-merge-fast.png)

可以看到 `Fast-Forword` 算法的优点是分支很干净，就是一条直线；缺点是我们看不出分支是从哪个节点合入的。我们可以使用 `git merge --no-ff` 明确不使用 `Fast-Forword` 方法。

> **git reset**
>
> git reset 回退主要有 3 种用法：
>
> - git reset <文件路径> 将指定文件从暂存区移除，文件状态会改为待提交
> - git reset --soft <提交节点> 将当前分支的 HEAD 回退到指定节点，被回退节点上修改的文件状态变为待提交；
> - git reset --har <提交节点> 将当前分支的 HEAD 回退到指定节点，被回退节点上修改的文件会丢失。

我们先把代码使用 `git reset --hard` 回退到合并前 c2 ：

```sh
$ git reset --hard 2a8def6
HEAD is now at 2a8def6 c2
$ git log --oneline --graph --decorate --all
* f6b44a1 (hotfix/authors) c4
* 8121a15 c3
* 2a8def6 (HEAD -> main) c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

然后不使用  `Fast-Forword`  算法重新合并：

```sh
$ git merge --no-ff -m c5 hotfix/authors
Merge made by the 'ort' strategy.
 2 files changed, 2 insertions(+)
 create mode 100644 authors/relation.java
```

> **`ort` 策略**
>
> 可以看到采用了`ort` 策略，这是 Git 2.33 版本新引入的一种合并策略，用于优化 `Three-Way` 算法。Git 支持了很多种策略，关于策略的话题，我们后面详细解释，这里大家只要知道采用了 `ort` 策略说明合并必然使用的是 `Three-Way` 算法。

我们查看一下提交日志：

```sh
$ git log --oneline --graph --decorate --all
*   34650c4 (HEAD -> main) c5
|\
| * f6b44a1 (hotfix/authors) c4
| * 8121a15 c3
|/
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

可以看到 `Three-Way` 合并算法新生成了一个合并节点 c5 。

#### 算法二：Three-Way

接上文，其实 `Three-Way` 算法最常见的是两个合并的分支发生分叉时。我们再把 `HEAD` 回退到 c2 节点，然后在 `main` 分支继续提交 2 个节点 c5 和 c6，提交日志会变为：

```sh
$ git reset --hard 2a8def6
// 在 main 分支新增两个提交 c5 c6，此处操作省略
$ git log --oneline --graph --decorate --all
* 72286b0 (HEAD -> main) c6
* dbd2ec2 c5
| * f6b44a1 (hotfix/authors) c4
| * 8121a15 c3
|/
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

流程图示可以更形象的描述为：

![](images/git-origin-branch-2.png)

我们也可以在 `GitHub` 网页上查看 `Network graph` ：

<img src="/Users/imangozhang/Library/Application Support/typora-user-images/image-20230419150704328.png" alt="image-20230419150704328" style="zoom:50%;" />

此时 `git merge` 会采用 `Three-Way` 算法进行合并，会新生成一个合并节点：

```sh
$ git merge hotfix/authors
Merge made by the 'ort' strategy.
$ git log --oneline --graph --decorate --all
*   f42c98c (HEAD -> main) c7
|\
| * f6b44a1 (hotfix/authors) c4
| * 8121a15 c3
* | 72286b0 c6
* | dbd2ec2 c5
|/
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

用流程图表示为：

![](images/git-origin-merge.png)

为什么命名为 `Three-Way` 呢？

假如两个分支同时对一个文件做了修改，那么工具在帮我们做合并时，只知道两个文件在同一行上有差异，却没办法知道在合并后的版本里，到底该保留谁的版本，所以只能交给用户自己手工来决定。这就是普通的 Merge 工具所能做的。

![](images/three-way.png)

和普通的 Merge 工具相比，Git 最大的不同在于它记录了文件的提交历史，因此可以向前回溯文件修改前的“原件”。它在合并时不仅会看两人各自的文件内容，还会看之前的原件。通过和原来版本的对比，就可以清楚地知道，第一行应该保留**子节点1**的版本，因为**子节点2**的版本和原件相比并没有变化。这一过程可以由工具自动完成，而不用像 `Two-Way` Merge 那样，需要交给用户手工来决定，这就是 `Three-Way` Merge。

`Three-Way` 合并算法的优点是可以通过提交日志直观看出各分支合并情况，但是当我们的分支很多的时候，提交历史就会变的很臃肿很乱。想要解决这一问题就可以采用下面的 **`Squash`** 算法或者**基变**。

#### 算法三：Squash

所谓Squash Merge，是指Git在做两个分支间的合并时，会把被合并分支（通常被称为topic分支）上的所有变更“压缩（squash）”成一个提交，追加到当前分支的后面，作为“合并提交”（merge commit）。

> 从参与合并的文件变更上来说，`Squash` Merge 和普通 Merge 并没有任何区别，效果完全一样。唯一的区别体现在提交历史上：正如我们前面提到的，对于普通的Merge而言，在当前分支上的合并提交通常会有两个 parent；而 Squash Merge 却只有一个。

我们首先在 main 分支执行 git reset 回退到 c5：

```sh
$ git reset --hard dbd2ec2
$ git log --oneline --graph --decorate --all
* dbd2ec2 (HEAD -> main) c5
| * f6b44a1 (hotfix/authors) c4
| * 8121a15 c3
|/
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

现在，让我们执行一次Squash Merge，把 hofix/authors 分支的内容合并到 main 分支：

```sh
$ git merge --squash hotfix/authors
$ git log --oneline --graph --decorate --all
```

然后创建新的提交：

```sh
$ git status
On branch main
Your branch is ahead of 'origin/main' by 3 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   authors/authors.java
	new file:   authors/relation.java
$ git commit -m "c6"
```

现在的提交历史是这样的：

```sh
$ git log --oneline --graph --decorate --all
* a2c47e2 (HEAD -> main) c6
* dbd2ec2 c5
| * f6b44a1 (hotfix/authors) c4
| * 8121a15 c3
|/
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

`Squash` Merge不会像普通 Merge 那样在合并后的提交历史里保留被合并分支的分叉，被合并分支上的提交记录也不会出现在合并后的提交历史里，所有被合并分支上的变更都被“压缩”成了一个合并提交。

如果在被合并分支上，完整的提交历史里包含了很多中间提交（`intermediate commit`），比如：改正一个小小的拼写错误可能也会成为一个独立的提交，而我们并不希望在合并时把这些细节都反应在当前分支的提交历史里。这时，我们就可以选择 Squash Merge。

在执行完 `git merge --squash` 之后，所有提交都被合并到 `main` 分支，此时 `hotfix` 分支就可以直接删除了，这样提交记录就是一条执行，非常干净。

### 合并策略

默认情况下，Git在合并分支时会自动选择最合适的 Merge 策略，我们也可以通过参数`-s`显式指定策略。不同的策略会对合并的方式与结果产生不同的影响，比如：对参与合并的分支，其“共同祖先（`common ncestor`）”的选择，以及在合并过程中发生冲突时的处理方式等。

#### 策略一：Recursive

`Recursive` 策略只适用于两个分支之间的合并。因此，对于超过两个分支的合并，需要反复地进行两两合并，才能最终完成所有分支的合并（这也是Recursive名字的由来）。本质上，`Recursive` 就是一种`Three-Way` Merge。

**它的特点在于，如果Git在寻找共同祖先时，在参与合并的两个分支上找到了不只一个满足条件的共同祖先，它会先对共同祖先进行合并，建立临时快照。然后，把临时产生的“虚拟祖先”作为合并依据，再对分支进行合并。**

如下图所示，在对两个分支上的提交A和B进行合并时，我们发现了它们有两个共同祖先，分别是：ancestor0和ancestor1。这个时候，Recursive策略会对ancestor0和ancestor1进行合并，临时创建一个虚拟祖先：ancestor2。用ancestor2与A，B一起进行Three-Way Merge。

![](images/recursive.png)


> **Criss-Cross 现象：**
>
出现多个满足条件的共同祖先的现象被称为 `Criss-Cross` 现象，对于那些存在时间很长，拓扑结构很复杂的分支经常出现这种现象。

- Ours/Theirs 参数

在处理合并时，和其他某些 Merge 策略一样，Recursive 策略通常会尽量自动完成合并。如果在合并过程中发现冲突，Git会在被合并的文件里插入冲突标记（merge conflict markers），并标记当前文件存在冲突，然后交由人工来处理。

不过，我们也可以通过指定参数告诉 Git，当发生冲突时自动选择或丢弃其中一个分支上的修改。比如，假设我们要把分支 B 合并到分支 A 。如果指定参数`-Xours`，则表明丢弃分支 B 上的修改，保留当前分支A上的内容；指定参数`-Xtheirs`则刚好相反。

这里要注意的是，这两个参数只在发生冲突时起作用。而正常情况下，即没有发生冲突时，Git还是会帮我们自动完成合并的。关于这一点，在后面谈到Ours策略时可以做一个对比，两者在概念上比较容易混淆。

### 策略二：Resolve

和 `Recursive` 策略类似，`Resolve` 策略是另一种解决两个分支间合并问题的策略，同样也是采用的 `Three-Way` Merge 算法，是在 Recursive 策略出现之前，Git 合并时所采用的默认策略。**和 Recursive 策略不同的是，它在处理 Criss-Cross 情况时，会在多个满足条件的共同祖先里选取其中一个作为合并的基础（称为 Merge Base ）。**在某些情况下，如果使用 Recursive 策略作为默认策略进行合并时遇到了问题，也可以尝试选择 Resolve 策略。

### 策略三：Ours

前面提到了，如果在使用 `Recursive` 策略时指定`-Xours`参数，那么当发生冲突时，Git会选择丢弃来自被合并分支的修改，而保留被当前分支上的原有修改。这种情况只在有冲突时才会发生，如果没有冲突，Git还是会帮我们自动完成合并的。

与之不同的是，`Ours` 策略无论有没有冲突发生，都会毫不犹豫的丢弃来自被合并分支的修改，完整保留当前分支上的修改。所以，对于 `Ours` 策略而言，实质上根本就没有做任何真正意义上的合并，或者说做的是假合并（`fake merge`）。不过，从提交历史上看，Git依然会创建一个新的合并提交（`merge commit`），并让它的 `parent` 分别指向参与合并的两个分支上的提交记录。

#### 策略四：Ort

[`Ort` 策略](https://git-scm.com/docs/merge-strategies) 才是 Git 合并策略中的主角，因为这是自 Git 2.33 版本之后采用的默认合并策略。 `Ort` 策略同样也是采用的 `Three-Way` Merge 算法，该策略和 `Recursive` 策略基本类似，相比之下有两个优势：

1. 合并速度更快，原来复杂和合并如果使用 `Recursive` 策略耗时 5s 以上，但是采用`Ort` 策略只需要不到 200ms；
2. 修复了 `Recursive` 策略中的一些细节错误。
## 基变

我们在上面讲到 `Three-Way` 合并算法时提到，当两个分支出现分叉后使用 merge 合并又会产生一个新的提交节点。其实，还有一种方法：你可以提取在 `c7` 中引入的补丁和修改，然后在 `c4` 的基础上应用一次。 在 Git 中，这种操作就叫做 **变基（rebase）**。 你可以使用 `rebase` 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样。

我们还是在 main 分支执行 git reset 回退到 c5：

```sh
$ git reset --hard dbd2ec2
$ git log --oneline --graph --decorate --all
* dbd2ec2 (HEAD -> main) c5
| * f6b44a1 (hotfix/authors) c4
| * 8121a15 c3
|/
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 (origin/main, origin/HEAD) Initial commit
```

然后执行 `git rebase` ：

```sh
$ git rebase main
$ git log --oneline --graph --decorate --all
* 34dbfc6 (hotfix/authors) c4
* 9ec24bf c3
* dbd2ec2 (HEAD -> main) c5
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 Initial commit
```

可用看到，通过 `rebase` 合并后，并没有生成新的提交，而是把 `c3` 和 `c4` 两个提交直接拼接到了 `c5` 之后。它的原理是首先找到这两个分支（即当前分支 `experiment`、变基操作的目标基底分支 `master`） 的最近共同祖先 `c2`，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件， 然后将当前分支指向目标基底 `c5`, 最后以此将之前另存为临时文件的修改依序应用。 可用用流程图描述为：

![](images/rebase.png)

这个图是不是有些熟悉？是不是和执行 `Fast-Forward` 之前的流程图一样？！所以接下来我们进行一下 `Fast-Forward` 合并，`HEAD` 和 `main head` 指针是不是就移动到 `c4` 了？

```sh
$ git merge hotfix/authors
Updating dbd2ec2..34dbfc6
Fast-forward
$ git log --oneline --graph --decorate --all 
* 34dbfc6 (HEAD -> main, hotfix/authors) c4
* 9ec24bf c3
* dbd2ec2 c5
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 Initial commit
```

结果必然是这样！

## 提交拷贝

我把 `git cherry-pick` 命令描述为“提交拷贝”，因为我觉得它的功能就是把某个分支的一个（或多个）提交拷贝到当前分支。例如我们的提交记录如下：

```sh
$ git log --oneline --graph --all
* c025b9d (HEAD -> main) c7
| * 6bd8367 (feature/books) c6
|/
* 34dbfc6 (origin/main, origin/HEAD) c4
* 9ec24bf c3
* dbd2ec2 c5
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 Initial commit
```

我们想把 `feature/books` 上的提交 `c6` 复制到 `main`分支：

```sh
$ git cherry-pick 6bd8367
$ git log --oneline --graph --all
* a46965b (HEAD -> main) c6
* c025b9d c7
| * 6bd8367 (feature/books) c6
|/
* 34dbfc6 (origin/main, origin/HEAD) c4
* 9ec24bf c3
* dbd2ec2 c5
* 2a8def6 c2
* 3c47a32 c1
* fbe2f09 Initial commit
```

可以看到 `c6` 提交被拷贝到了 `main` 分支上。加入执行 `git cherry-pick` 过程中发生了冲突，需要手动解决冲突，然后 `git cherry-pick --continue` 继续合并即可。

`git cherry-pick` 非常适用于我们有些分支已经严重落后于主干分支或开发分支了，但是这个又是一个为某个客户服务的固定版本，此时这个分支上发现了一个 bug，这个 bug 在主干上同样存在。此时我们就可以把 bug fix 的节点提交到主干验证没有问题后，将该修复通过 `cherry-pick` 拷贝到落后分支上。

# 4.业界规范

业界对分支的管理、代码的提交和合并，都有一套规范，主要有三个目的：

- 更简洁、更统一的做好好源码管理 

- 保证提交历史清晰可读
- 更有效的保证产品质量

下面汇总了各互联网行业主要大厂的一些常用规范，主要适用（但不限于）：

- 架构师
- 开发工程师
- 测试工程师
- ……

## 分支管理规范

### 分支分类

我们源码仓库的分支可以按照**生命周期**和**用途**来划分，不同类型的分支命名规范如下：

![](images/分支分类.png)

### 分支用途

![](images/features.png)

- **master（发布分支）**
  - 与生产环境保持一致
  - 发现生产环境问题必须以 master 为基准创建 `hotfix/*` 分支修复
- **develop（开发分支）**
  - 所有新功能都在此分支开发
  - 所有分支中功能最全、代码最新的一个分支
- **feature/*（新功能开发分支）**
  - 命名规则：feature/[功能名称]
  - 只能从 develop 创建
  - 开发测试后重新合并到 develop 分支
- **release/*（预发布分支）**
  - 命名规则：release/v + [版本号]
  - 只能从 develop 创建
  - 同一时间点只能存在一个预发布分支
  - 上一个版本发布成功后必须删除
- **hotfix/*（热修复分支）**
  - 命名规则：hotfix/v + [bug 修复版本号]
  - 只能从 master 创建，仅用于生产环境发现的 bug
  - 修复最终要合并到 develop 及 master，并删除该分支

### 分支保护

根据项目管理级别分为两类保护策略:

- **通用策略**： **master** **分支** 由管理角 色 Master 负责管理， 其他人员无权限修改；
- **严控策略** ：**master** **和** **develop** **分支** 均由管理角色 Master 负责管理， 其他人员无权限修改

## 最佳实践

### 功能开发

- **标准流程**

开发新功能都应遵循以下流程:

1. 拉取最新的 develop 分支，然后以最新的 develop 为基准创建新的 feature 分支
2. 在各 feature 分支上进行开发、测试...
3. 合并 feature 至 develop，并删除 feature

```sh
$ git pull origin develop
$ git checkout -b feature/f1 develop
$ git checkout develop
$ git merge --no-ff feature/f1
$ git branch -d feature/f1
```

![标准开发流程](images/feature-standard.png)

- **最佳实践**

1. 每一个 feature/* 应与 TAPD 上的 feature 有明确的对应关系(与 issue 对应更佳)
2. 使用 **git flow** 可以更简单的完成分支管理:

```sh
# 1. 初始化 git flow 
$ git flow init

# 2. 以 develop 为基准创建 feature/f1 
$ git flow feature start f1

# 3. feature/f1 开发和测试完毕，合并 develop，并删除分支 feature/f1 
$ git flow feature finish f1
```

### 发布与预发布

- **标准流程**

提交到预发布和生产环境应遵循以下流程:

```sh
# 1. 从 develop 分支创建新的预发布分支 release # 并部署到预发布环境上测试
$ git checkout -b release/v0.1.0 develop

# 2. 预发布分支完全测试通过后
# 2.1 合并回 develop 与 master
$ git checkout develop
$ git merge --no-ff release/v0.1.0
$ git checkout master
$ git merge --no-ff release/v0.1.0 
# 2.2 打上正式版本的 tag
$ git tag v0.1.0
# 2.3 删除预发布分支 release/v0.1.0 
$ git branch -d release/v0.1.0
```

![部署流程规范](images/deploy-standard.png)



- **Bug 热修复流程**

生产环境发现 Bug，需要 Hotfix 时应遵循以下流程:

```sh
# 1. 分离热修复分支，在分支上进行修复
$ git checkout -b hotfix/v0.1.1 master
# 2. 分支验证通过后，重新合并回 develop 和 master 
$ git checkout develop
$ git merge --no-ff hotfix/v0.1.1
$ git checkout master
$ git merge --no-ff hotfix/v0.1.1
# 3. 在 master 上打一个热修复 tag
$ git tag v0.1.1
# 4. 删除 hotfix/v0.1.1
$ git branch -d hotfix/v0.1.1
```

- **最佳实践**

使用 git flow 进行 预发布和发布管理:

```sh
# 1. 开始新功能 f1 的预发布验证，版本定为 0.1.0
$ git flow release start v0.1.0
# 2. 新功能 f1 预发布验证通过
# 合并到 master，打 tag，合并到 develop，删除 relase 分支
$ git flow release finish v0.1.0
```

使用 git flow 进行 Hotfix 管理:

```sh
# 1. 开始在线 v0.1.0 的 bug 的修复，以 master 为基准创建 hotfix/v.0.1.1 
$ git flow release start v0.1.1
# 2. bug 修复完成
# 合并到 master，打 tag，合并到 develop，删除 hotfix 分支
$ git flow hotfix finish v0.1.1
```

## 提交与合并规范

### 代码提交规范

- git commit 结构

目前规范使用较多的是 [Angular 团队的规范](https://link.juejin.cn/?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fgithub.com%2Fangular%2Fangular.js%2Fblob%2Fmaster%2FDEVELOPERS.md%23-git-commit-guidelines), 继而衍生了 [Conventional Commits specification](https://link.juejin.cn/?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%3A%2F%2Fconventionalcommits.org%2F)，继而衍生出很多 git 提交的工具，其中有一款非常好用的推荐给大家：[git-cz](https://www.npmjs.com/package/cz-git)，按照教程安装和使用，可以规范回你的提交内容。现在 git 提交规范基本一致：

```sh
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

1. type type为必填项，用于指定commit的类型，约定了feat、fix两个主要type，以及docs、style、build、refactor、revert五个特殊type，其余type暂不使用。

| 主题  | 用途           | 主题     | 用途                     |
| ----- | -------------- | -------- | ------------------------ |
| feat  | 新功能         | style    | 格式                     |
| test  | 增加测试       | chore    | 构建过程或辅助工具的变动 |
| fix   | 修补 bug       | refactor | 重构                     |
| build | 编译相关的修改 | revert   | 回滚上一次 commit        |
| docs  | 文档           | perf     | 性能优化                 |
| ci    | CI 相关的修改  |          |                          |

当一次改动包括主要type与特殊 type 时，统一采用主要 type 。

2. scope scope也为必填项，用于描述改动的范围，格式为项目名/模块名，例如： node-pc/common rrd-h5/activity，而we-sdk不需指定模块名。如果一次commit修改多个模块，建议拆分成多次commit，以便更好追踪和维护。
3. body body填写详细描述，主要描述改动之前的情况及修改动机，对于小的修改不作要求，但是重大需求、更新等必须添加body来作说明。
4. break changes break changes 指明是否产生了破坏性修改，涉及break changes的改动必须指明该项，类似版本升级、接口参数减少、接口删除、迁移等。
5. affect issues affect issues 指明是否影响了某个问题。例如我们使用jira时，我们在 commit message 中可以填写其影响的 JIRA_ID，若要开启该功能需要先打通jira与gitlab

每次提交建议添加关键词前缀，用于指示本次改动的主题。

### 代码合并规范

`Merge Request(MR)`（或者 `Pull Request(PR)`）规范

- 建议在提交时需在标题中添加 **[MR]** 前缀
- 不要在一个 MR 中包含多个主题
- 每个 MR 提交人必须指定一名 `Code Reviewer` 进行代码审查、合并

# 附：Git 命令汇总

| 命令                                        | 详情                                                         |
| ------------------------------------------- | ------------------------------------------------------------ |
| git clone <远程仓库地址>                    | 克隆项目到本地（Https/SSH/GitCLI）                           |
| git init                                    | 在目录中创建新的 Git 仓库                                    |
| git status                                  | 查看文件状态                                                 |
| git diff <文件路径>                         | 查看文件的变更内容                                           |
| git add <文件路径>                          | 添加文件到暂存区，文件路径支持正则匹配                       |
| git commit -m <提交说明>                    | 提交暂存区文件到本地仓库                                     |
| git fetch [指令] [<仓库地址>] [<分支>]      | 同步远程仓库内容：<br>默认：同步远程分支内容到本地<br>--all：同步远程仓库所有内容到本地 |
|                                             |                                                              |
| git pull [<仓库地址>] [<分支>]              | 同步远程分支内容到本地，并与当前分支合并                     |
| git push [<仓库地址>] [<分支>]              | 推送本地仓库的当前分支到远程仓库                             |
| git remote [指令] [<远程仓库地址>]          | 管理远程仓库信息：<br>-v：查询远程仓库地址；<br>add：添加远程仓库地址<br/>set-url：更新远程仓库地址<br>remove：删除远程仓库地址 |
| git log [指令]                              | 查看提交历史：<br>默认：查看本地仓库当前分支提交历史<br>--oneline --graph --decorate --all：查看所有分支提交历史 |
| git reset [指令] <提交节点/文件路径>        | 撤销/回退：<br>默认 文件路径：撤销暂存区的某个文件<br>默认 HEAD^：本地提交回退到上一次提交的版本<br>--soft 提交节点：本地提交回退到指定提交版本<br>（撤回的文件状态都改为待提交状态）<br>--hard 提交节点：当前分支会退到某个历史提交节点<br>（撤回的文件直接丢弃，谨慎使用） |
| git reblog                                  | 查看本地仓库的所有操作日志，可用于恢复误操作导致的代码丢失   |
| git checkout [指令] [<文件路径>/<分支名称>] | 文件管理/分支管理：<br>直接加文件路径：撤销指定文件的修改<br>直接加分支名称：切换到指定分支<br>-b 分支名称：新建分支 |
| git branch [指令] [<分支名称>]              | 分支管理：<br>-a：查看本地仓库的所有分支<br>-d 分支名称：删除本地仓库指定分支<br>-D 分支名称：强制删除本地仓库指定分支 |
| git rebase <目标分支名称>                   | 以基变的方式合并分支                                         |
| git merge <被合并分支名称>                  | 合并分支，可能                                               |
| git cherry-pick <提交节点>                  | 将指定的某个分支的一个（或多个）提交拷贝到当前分支           |