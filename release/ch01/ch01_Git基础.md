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
#
```
2. 克隆 Git 仓库


































***
