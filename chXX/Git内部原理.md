# Git 内部原理
## 一、Git 对象
### 1> Git 对象的存储与取回
1. 初始化一个 test 目录
    ```
    $ git init test
    $ cd test
    $ find .git/objects
    # 只有目录：pack, info
    $ find .git/objects -type -f
    # 没有文件
    ```
2. 向 Git 数据库存入一些文本
    ```
    $ echo 'test content' | git hash-object -w --stdin
    $ find .git/objects -type -f
    # 查看文件
    ```
3. 从 Git 中取出数据
    ```
    # 在 test 目录下执行
    $ git cat-file -p d670...
    # test content
    ```
### 2> 简单的版本管理
1. 编辑一个文件 test.txt 存入 Git 数据库
    ```
    # 创建 test.txt 文件，并加入初始内容
    $ git hash-object -w test.txt
    $ find .git/objects -type f
    # 查看到 test.txt 文件的文件名(sha-1校验和)
    ```
2. 对 test.txt 进行修改，再次存入数据库
    ```
    # 修改 test.txt 文件，保存
    $ git hash-object -w test.txt
    $ find .git/objects -type f
    # 查看到 test.txt 文件的两个版本的文件名(sha-1校验和)
    ```
3. 恢复到 test.txt 的第一个版本
    ```
    $ git cat-file -p 第一个版本的校验和 > text.txt
    # 打开观察 test.txt，看到第一个版本
    ```
4. 恢复到 test.txt 的第二个版本
    ```
    $ git cat-file -p 第二个版本的校验和 > text.txt
    # 打开观察 test.txt，看到第二个版本
    ```
5. 问题
以上管理方式的问题——只保存了数据，没有保存文件名
我们可以查看文件的类型是数据对象：blob object
    ```
    $ git cat-file -t 文件校验和
    blob
    ```

## 二、树对象
### 1> 查看树对象
```
# 创建目录 dir，并在目录中创建文件 content.txt
$ git add dir
$ git commit
# 查看刚添加的树对象
$ git cat-file -p master^{tree}
```

### 2> 创建树对象
1. 将 test.txt 版本一写入树对象
    ```
    # 将 test.txt 版本一加入暂存区
    $ git update-index --add --cacheinfo 100644 f99XXX test.txt

    # 将暂存区内容写入树对象
    $ git wirte-tree  # 返回树对象校验和：1b8cXXX

    # 查看树对象和类型
    $ git cat-file -p 1b8cXXX     # 显示树对象内容
    $ git cat-file -t 1b8cXXX
    $ tree
    ```
2. 将  test.txt 版本二和一个新文件 new.txt 写入新的树对象
    ```
    # 创建文件 new.txt
    # 将 new.txt 加入暂存区
    git update-index --add new.txt

    # 将暂存区中的 test.txt 更新为版本二
    git update-index --cacheinfo 100644 0b6XXX test.txt

    # 将暂存区内容写入树对象
    git write-tree # 返回树对象校验和：6129XXX

    # 查看树对象
    git cat-file -p 6129XXX
    git cat-file -t 6129XXX
    ```

3. 将第一个树对象作为子目录加入第二个树对象
    ```
    # 读入原树对象作为当前树对象的子目录 bak
    git read-tree --prefix bak 1b8cXXX

    # 将修改后的当前树对象写入新的树对象
    git write-tree  # 返回树对象校验和：6ff2XXX
    # 查看新树对象
    git cat-file -p 6ff2XXX   # 显示 bak, new.txt, test.txt
    ```

4. 提交对象
    ```
    # 提交第一个树对象
    $ echo 'first commit' | git commit-tree 1b8cXXX   # 返回提交对象的校验和：93a9XXX
    # 查看提交对象
    $ git cat-file -p 93a9XXX

    # 创建第二个提交对象，将 test.txt 版本二提交，让上个提交对象作为父对象
    $ echo 'second commit' | git commit-tree 6129XXX -p 93a9XXX
    # 返回提交对象 sha-1:5ee637d0XXX
    # 创建第三个提交对象，将二者合并提交，让上个提交对象作为父对象
    $ echo 'third commit' | git commit-tree 6ff2XXX -p 5ee637d0XXX
    # 返回提交对象 sha-1：bd0e04XXX
    ```

5. 查看信息
    ```
    # 查看提交 log
    $ git log --stat bd0e04
    # 查看所有的对象
    $ find .git/objects -type f
    ```

## 三、Git 引用
1. 创建一个引用
```
# 创建一个引用
$ echo "5ee637XXX" > .git/refs/head/master
# 在 Git 命令中使用刚创建的引用来代替 SHA-1 值
$ git log --pretty=oneline master
```
2. 修改引用
```
# 不提倡直接修改引用文件，应使用 update-ref 命令
$ git update-ref refs/heads/master
```
## 四、HEAD 引用
1. 查看 HEAD 引用
```
# Linux 命令
$ cat .git/HEAD
# git 命令
$ git symbolic-ref HEAD
```
2. 更新符号引用
```
# 更新
$ git symbolic-ref HEAD refs/heads/test
# 查看
$ git symbolic-ref HEAD
```

## 五、标签引用（标签对象）

1. 创建轻量标签
```
# 创建
git update-ref refs/tags/v1.0 a0b2dXXX
# 查看
cat .git/refs/tags/v1.0
```
2. 创建标注标签
```
# 创建
$ git tag -a v1.1 a0b2dXXX -m '重要版本'
# 查看标注标签对象的 SHA-1
$ cat .git/refs/tags/v1.1
# 查看标注标签内容
$ git cat-file -p XXXX
```


## 六、远程引用

## 七、包文件
```
git gc
```













***
