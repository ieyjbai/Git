# 第一章 Git基础


## 一、什么是 Git ？
**Linus** 的第二个伟大作品。2005年由于BitKeeper软件公司对Linux社区停止了免费使用权。Linus迫不得已开发自己的版本控制工具，基于使用 BitKeeper 时的经验教训，开发出了 Git。
目前，Eclipse上使用Git的项目数量也已经超过了使用SVN的仓库数。

### Git 的设计目标
1. 速度快：所有的操作都可以是离线操作，所以速度非常快；
2. 简单的设计：Git 本质上是一个基于内容寻址的文件管理系统，底层概念非常简单；
3.  对非线性开发模式的强力支持：使用方式种类繁多，可自行创造;
4. 有能力高效管理类似 Linux 内核一样的超大规模项目（速度和数据量）;
5. 完全分布式：客户端并不只提取最新版本的文件快照，而是把代码仓库完整地镜像下来。 这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。 因为每一次的克隆操作，实际上都是一次对代码仓库的完整备份。
    ![distributed](images/distributed.png)

## 二、Git 的基本思想和工作原理
### 1. 直接记录快照，而非差异比较
Git 与其他版本控制系统的重要差别在于 Git 对待数据的方法。

**其他的版本控制系统 (SVN)** 存储 **数据的差异**，即：存储一组 base 文件，以后每次存储 base 文件的更改。
![delta](images/deltas.png)

**Git** 存储 **快照**，即：每次更改都存储一个新文件。 为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。
![snapshots](images/snapshots.png)

### 2. 近乎所有操作都是本地执行
Git 在本地磁盘上存储项目的完整历史，所以大部分操作看起来瞬间完成。这也意味着你离线时，几乎可以进行任何操作。

### 3. Git 保证完整性
1. 在 Git 的底层，所有数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情时更改任何文件内容或目录内容；
2. Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）。 它根据文件的内容或目录结构计算出一个由 40 个十六进制字符（0-9 和 a-f）组成的字符串，如下所示：
    ```
    fbcb5d6c11cf6b92cc00b3ff78f182ea2c1b6d38
    ```
3.  实际上，Git 数据库中保存的信息都是以文件内容的 SHA-1 值来索引，而不是文件名。

### 4. Git 一般只添加数据
一旦你提交快照到 Git 中，就难以再丢失数据。

### 5. 文件的三种状态
Git 中的文件有三种状态：**已提交、已修改、暂存**。
由此引入 Git 项目的三个工作区域的概念：**Git 仓库、工作目录、暂存区域**。
[图xxx]

## 三、Git 的安装与配置
### 1. 在 Windows 上安装
 官方版本可以在 Git 官方网站下载。 打开 http://git-scm.com/download/win，下载会自动开始。 要注意这是一个名为 Git for Windows的项目（也叫做 msysGit），和 Git 是分别独立的项目。
  你可以在 GitHub for Windows 网站下载，网址为 http://windows.github.com。

### 2. 在 Mac 上安装
官方维护的 OSX Git 安装程序可以在 Git 官方网站下载，网址为 http://git-scm.com/download/mac。

### 3. 基本配置
1. 配置信息的存储位置
* `/etc/gitconfig` 文件：包含系统上每个用户及他们仓库的通用配置。Windows 下为：`Git 安装目录/minw64/etc/gitconfig`。
* `~/.gitconfig` 或 `~/.config/git/config` 文件：只针对当前用户有效，Windows 下为：`C:\Users\$USER\ .gitconfig`。
* 当前仓库的 `.git` 目录中的 `config` 文件(`.git/config`)：只针对该仓库有效。

2. 用户信息
当安装完 Git 应该做的第一件事就是设置你的用户名称与邮件地址。
    ```
    $ git config --global user.name "ieyjbai"
    $ git config --global user.email "ieyjbai@163.com"
    ```
    **注：** global 表示全局设置，只要是该用户登录，都是用此信息；不带参数，表示本项目使用该信息；--system 表示所有用户和项目都是用该信息。

3. 检查配置信息
    ```
    # 列出所有配置信息
    $ git config --list
    # 列出某一项配置
    $ git config user.email
    ```
### 4. 获取帮助
```
# 查询某命令(config)详情
$ git help config
# 或
$ git config --help
```

## 四、基本概念与应用

### 1> 获取 Git 仓库
1. 初始化 Git 仓库
    ```
    # 创建一个工作目录 myrep 进入，运行如下命令
    $ git init
    ```
    **注：** 看到目录 myrep 中出现 .git 目录，则表示创建仓库成功。
2. 克隆 Git 仓库到
    ```
    # 如果现有一个远程仓库：https://github.com/ieyjbai/barn.git
    # 运行如下命令
    $ git clone https://github.com/ieyjbai/barn.git
    ```
    **注：** 看到在当前文件夹下创建 barn 目录(工作目录)，其中包含仓库的文件即表示克隆成功。
### 2> 向仓库中加入一个新文件
工作目录中的文件只能处于 2 中状态：*已跟踪状态* 和 *未跟踪状态*
**已跟踪状态** 表示该文件已经被 Git 所管理。
**未跟踪状态** 表示该文件尚未被 Git 所管理。
**注：** 所有的文件都使用 utf-8 编码。
1. 首先，我们检查一下目前工作目录 barn 的状态
    ```
    $ git status
    On branch master  # 目前位于主分支(后面再讲)
    Your branch is up-to-date with 'origin/master'. # 你的分支是最新的

    nothing to commit, working tree clean # 没有要提交的数据，仓库很干净
    ```
    **说明：** 本状态说明工作目录很干净，要么你刚刚克隆了远程库，未做任何修改；要么你刚刚提交过数据。
2. 在工作目录下，创建一个文件 `first.txt`，写入一些内容；查看此时工作目录的状态
    ```
    $ git status

    On branch master
    Your branch is up-to-date with 'origin/master'.

    Untracked files:
      (use "git add <file>..." to include in what will be committed)

            first.txt

    nothing added to commit but untracked files present (use "git add" to track)
    ```
    **注：** 文件 first.txt 此时处于未跟踪状态
3. 将文件 first.txt 加入暂存区(暂存状态)
    ```
    # 加入文件
    $ git add first.txt
    # 查看状态
    $ git status
    On branch master
    Your branch is up-to-date with 'origin/master'.

    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   first.txt
    ```
    **说明：** 文件 first.txt 已被加入暂存区，可以被提交了。如果添加的是目录，则目录下的所有内容都会被加入暂存区。
4. 将文件提交到仓库，**只有暂存区中的文件才能被提交！**
    ```
    $ git commit
    # 此时，会打开文本编辑器 (vim)，让我们输入备注信息
    [master a86de8b] 添加 first.txt
     1 file changed, 1 insertion(+)
     create mode 100644 first.txt
    ```
    **说明：** 此时，文件 first.txt 被加入本地仓库。

### 3> 修改一个已跟踪文件
目前 first.txt 已处于被跟踪状态，只要被添加到暂存区，该文件就被跟踪了。我们可以继续修改文件。
```
# 1. 修改 first.txt

# 2. 查看状态
$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit: # 未被暂存的修改，可提交
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory

        modified:   first.txt

no changes added to commit (use "git add" and/or "git commit -a")
# 显示文件 first.txt 未被暂存，可以被提交。即：暂存的文件被修改后就进入已修改状态。

# 3. 把文件放回暂存区
$ git add first.txt

# 4. 查看状态
$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   first.txt
# 文件可以被提交了

# 5. 提交文件，更新仓库
$ git commit -m '添加 first.txt 文件' -v
# -m 表示直接写入备注信息；
# -v 表示将文件改动信息加入备注
$ git commit -m '修改first.txt，添加信息' -v
[master e17cf5c] 修改first.txt，添加信息
 1 file changed, 5 insertions(+), 1 deletion(-)
````

### 4> 文件的特殊状态：既处于暂存态又处于修改状态
我们再加入新的文件，再做更多的修改
```
# 1. 添加文件 second.txt
# 2. 修改 first.txt

# 3. 使用短格式查看状态[-s | --short]
$ git status -s
 M first.txt
?? second.txt
#  右 M 表示 first.txt 已修改且未放入暂存区
# ?? 表示 second.txt 未被跟踪

# 4. 将 first.txt 和 second.txt 添加到暂存区
$ git add first.txt second.txt

# 5. 查看状态
$ git status -s
M  first.txt
A  second.txt
# 左 M 表示 first.txt 已修改且已放入暂存区
#  A 表示 second.txt 是新文件

# 6. 继续修改 first.txt

# 7. 短格式查看状态
$ git status -s
MM first.txt
A  second.txt
# MM 表示该文件一部分被修改的被放入暂存区；另一部分被修改的没放入暂存区
# 此时提交，只有放入暂存区的修改才会被更新到数据库

# 8. 完整查看
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   first.txt
        new file:   second.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   first.txt

# 9. 再次将 first.txt 添加到暂存区，查看
$ git add first.txt
$ git status -s
M  first.txt
A  second.txt

# 10. 提交
$ git commit
vim 编辑备注信息
[master a3a2bcb] 提交 first.txt second.txt
 2 files changed, 12 insertions(+), 2 deletions(-)
 create mode 100644 second.txt
```

### 5> 忽略某些文件
如果在工作目录下，有些文件我们不想让 Git 来管理，那么，可设置为忽略文件。
设置方法：在工作空间根目录 下创建文件 `.gitignore`，其中填写忽略规则，语法如下：
1. `#` 表示注释
2. 可以使用标准的 glob(某种简化的正则表达式) 模式匹配。
3. 匹配模式可以以（/）开头防止递归；匹配模式可以以（/）结尾指定目录。
4. 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。
5.  使用两个星号 (*) 表示匹配任意中间目录，比如`a/**/z` 可以匹配 a/z, a/b/z 或 `a/b/c/z`等

GitHub 为我们提供了各种语言中通常忽略的文件列表：https://github.com/github/gitignore

**测试忽略文件**
```
# 1. 在工作目录添加文件 .gitignore，内容为：*.log
# 2. 添加文件 today.log
# 3. 查看状态
$ git status
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        .gitignore

nothing added to commit but untracked files present (use "git add" to track)
# 只看到了 .gitignore 文件是未跟踪文件，没有看到 today.log 文件

# 4. 将 .gitignore 文件添加并提交
$ git add .gitignore
$ git commit;
```

### 6> 查看已暂存的和未暂存的修改
有时，我们想查看详细的文件修改的情况，可使用 `git diff`命令，它通过比较工作区内容和暂存区内容以补丁方式显示文件差异。
```
# 1. 修改 second.txt 文件
# 2. 查看文件差异
$ git diff
diff --git a/second.txt b/second.txt
index e660cb8..cf33f16 100644
--- a/second.txt
+++ b/second.txt
@@ -1,2 +1,3 @@
-我的第二个文件
+***我的第二个文件

+测试 git diff

# 说明工作区中的 second.txt 文件被修改了。

# 3. 以 --cached 方式查看文件差异
$ git dif --cached
<空>
# 说明暂存区没有任何文件

# 4. 暂存 second.txt
$ git add second.txt

# 5. 查看文件差异
$ git diff
<空>
# 说明工作区的文件与暂存区文件没有差异

# 6. 以 --cached 方式查看文件差异
$ git diff --cached
diff --git a/second.txt b/second.txt
index e660cb8..cf33f16 100644
--- a/second.txt
+++ b/second.txt
@@ -1,2 +1,3 @@
-我的第二个文件
+*** 我的第二个文件

+测试 git diff

# 说明暂存区内容较前次提交有很多修改

# 6. 继续修建 second.txt 文件
# 7. 查看文件差异
$ git diff
diff --git a/second.txt b/second.txt
index cf33f16..0132725 100644
--- a/second.txt
+++ b/second.txt
@@ -1,3 +1,5 @@
 *** 我的第二个文件

 测试 git diff
+
+进一步测试 git diff
\ No newline at end of file

# 表示工作区文件有了修改

# 8. 以 --cached 方式查看文件差异
$ git diff --cached
diff --git a/second.txt b/second.txt
index e660cb8..cf33f16 100644
--- a/second.txt
+++ b/second.txt
@@ -1,2 +1,3 @@
-我的第二个文件
+*** 我的第二个文件

+测试 git diff

# 暂存区内容与前次相同，说明没有新的修改加入暂存区，如果现在提交，只会提交暂存区的这些修改。

# 9. 暂存并提交 second.txt
$ git add second.txt
$ git commit
```

### 7> 移动/改名操作
Git 并不显式跟踪文件移动操作，但他能识别出移动操作
```
# 1. 将 first.txt 改为 first2.txt
$ git mv first.txt first2.txt
# 查看状态
$ git status
On branch master
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        renamed:    first.txt -> first2.txt
# Git 清楚的知道文件重命名或移动了

# 2. 提交
$ git commit
[master 56f2cef] 修改 first.txt > first2.txt
 1 file changed, 5 insertions(+), 1 deletion(-)
 rename first.txt => first2.txt (66%)
```
**注：** 处于已跟踪状态的文件都可以使用 mv 命令

### 8> 移除文件
要从 Git 中移除某个文件，就必须使用 `git rm` 命令从暂存区域移除，并连带从工作目录中删除指定的文件，然后提交。
如果只是简单地从工作目录中手工删除文件，运行 git status 时就会在 “Changes not staged for commit” 部分（也就是 未暂存清单）看到
```
# 1. 直接删除 second.txt(由于刚提交过，该文件不在暂存区)
# 2. 查看状态
$ git status
On branch master
Your branch is ahead of 'origin/master' by 5 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    second.txt

no changes added to commit (use "git add" and/or "git commit -a")

# 仍能看到该文件信息，提醒要么 add，要么 rm

# 3. 使用 git rm
$ git rm second.txt
rm 'second.txt'

# 4. 查看状态
$ git status
On branch master
Your branch is ahead of 'origin/master' by 5 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    second.txt
# 可以看出文件被删除，暂存区也看不到了

# 5. 提交
$ git commit
[master e60d677] 删除 second.txt
 1 file changed, 5 deletions(-)
 delete mode 100644 second.txt

# 从本地仓库里删除 second.txt

# 6. 删除一个在暂存区中的文件
# 把刚才删除的 second.txt 恢复，并添加到暂存区
$ git add second.txt
# 删除 second.txt
$ git rm second.txt
error: the following file has changes staged in the index:
    second.txt
(use --cached to keep the file, or -f to force removal)
# 暂存区中的文件是不能直接删除的，因为它还没有放入仓库，这样设计是为了防止误删。

# 7. 使用 `-f` 参数强制删除 second.txt 文件
$ git rm second.txt -f
rm 'second.txt'
# 工作目录中的 second.txt 自动被删除

# 8. 把文件从暂存区删除，在工作空间仍然保留
# 修改 first.txt
# 将 first.txt 添加到暂存区
$ git add first.txt
# 把文件从暂存区删除，并仍保留在工作空间
$ git rm --carm 'first.txt'
rm 'first.txt'

# 查看状态
$ git rm --cached first.txt
rm 'first.txt'

Administrator@PC-201612111337 MINGW64 /d/git/course/ch01/barn (master)
$ Git status
On branch master
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    first.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        first.txt

# first.txt 文件已成为未跟踪状态

```
**操作清单：**
1. 如何删除一个不在暂存区的文件？
2. 如何删除一个在暂存区中的文件？
3. 如何删除一个在暂存区中的文件，而在工作区保留？



### 9> 撤销操作
1. 覆盖上次提交

2. 

### 10> 查看提交历史

### 11> 打标签

### 12> 起别名



















***
