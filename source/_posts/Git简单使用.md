---
title: Git的常见使用
date: 2019-03-12 20:28:50
tags: 
- Git
categories:
- Git
---


Git的常见使用

---

## 配置GitHub

```shell
git config --global user.name "your_name"
git config --global user.email "your_email@example.com"
```

在windows系统中的`C:\Users\用户名\.ssh`中会产生`.ssh`文件夹中会生成一对密钥包括`id_rsa`私钥和`id_rsa.pub`公钥文件

如果不存在，在Git Bash使用以下命令生成

```shell
ssh-keygen -t rsa -C "your_email@example.com"
```

将会弹出以下信息

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/用户名/.ssh/id_rsa):  回车
Created directory '/c/Users/用户名/.ssh'.
Enter passphrase (empty for no passphrase):    回车或输密码
Enter same passphrase again:                   回车或再一次输密码
Your identification has been saved in /c/Users/用户名/.ssh/id_rsa
Your public key has been saved in /c/Users/用户名/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:JAldGP+8XXXXXXXXXXXRhzaEpUd5hJfTn0zrJxhXbFYM your_email@example.com
The key's randomart image is:
+---[RSA 3072]----+
|    ..o=*..   .o |
|     +o= +   E  o|
|    . + = . . . .|
|     . B + o . +.|
|    + O S o o + .|
|   = @ o . = =   |
|  . B o   o B    |
|     o     . .   |
|                 |
+----[SHA256]-----+
```

将`id_rsa.pub`公钥文件放到Setting中的SSH and GPG keys中

![GitHub](http://img.whl123456.top/image/GitHubSSH.png)

在Git Bash中输入以下命令，验证是否成功

```shell
ssh -T git@github.com
```

如果成功则会显示以下消息

```shell
Hi your_name! You've successfully authenticated, but GitHub does not provide shell access.
```

### 创建版本库

```shell
mkdir <文件夹名>
cd <文件名>
pwd
```

`git init` 把这个目录变成Git可以管理的仓库

结果生成.git 隐藏文件夹 （不要手动修改文件夹的内容）

> 注意事项
> 不要使用Windows自带的记事本编辑任何文件
> 默认编码设置成UTF-8 without BOM

![image-20200613103720281](http://img.whl123456.top/image/image-20200613103720281.png)

### 文件放到Git仓库目录下

`git status`

`git add <file>`

`git add .`提交新文件和被修改文件，不包括被删除文件

`git add -u`提交被修改和被删除文件，不包括新文件

`git add -A`提交所有变化

`git add -i`

> Unix哲学 “没有消息就是好消息”

### 把文件提交到仓库

`git commit -m <message>`-m 后面输入本次提交的说明

`git commit --amend`修改最近一次提交的注释信息

### vim编辑器的使用

`vi <filename>`

输入字母`i`或Insert键进入最常用的插入编辑模式
按下字母键 `c`（此时进入编辑状态），可以开始修改注释信息了
按下Esc (退出编辑状态)
接着连按两次大写字母`Z`，保存并退出
只想保存文件，则键入:`w`
放弃所有文件修改：按下ESC键进入命令模式，键入:`q!`回车后放弃修改并退出vi
`.`放弃所有文件修改，但不退出 vi:
按下ESC键进入命令模式，键入:`e!`，回车后回到命令模式

### 修改文件后

`git stautus` 掌握仓库当前的状态

> On branch master
> nothing to commit, working tree clean

`git diff <file>`#看具体修改了什么内容
再`git add <file>`

`git log`#历史记录 显示从最近到最远的提交日志  

`git log --pretty=oneline`oneline 压缩模式

### 退回之前的版本

就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100
`git reset --hard HEAD^`

`git reflog`#记录你的每一次命令
`git reset --hard <commit id>`（id不必写全）

### 修改

`git checkout -- <file>`
丢弃 工作区 的修改  在工作区的修改全部撤销，
让这个文件回到最近一次git commit或git add时的状态

`git reset HEAD <file>`
既可以回退版本，也可以把暂存区的修改回退到工作区

### 删除文件

`rm <file>`     #删除工作区文件
`git rm <file>` #从版本库中删除该文件

先手动删除文件，然后使用git add<file>效果是一样的
git checkout  -- <file>   #把误删的文件恢复到最新版本（用版本库里的版本替换工作区的版本）

### 分支

`git checkout -b <branchname>` #创建分支,然后切换到分支
`git branch <branchname>`      #创建分支
`git checkout <branchname>`    #切换到分支
`git branch`                   #查看当前分支
`git checkout master`          #换回master分支
`git merge <branchname>`       #合并指定分支到当前分支
`git branch -d <branchname>`   #删除分支
`git branch -m <branchname>`   #重命名分支

## GitHub

### 推送

关联一个远程库并推送

```shell
git remote add origin git@Lanqilu:path/<repo-name>.git
git push -u origin master
```

### 克隆

`git clone <url>`

### 拉取

`git pull`

1. 服务器代码合并本地代码

    ```shell
    git stash     //暂存当前正在进行的工作。
    git pull origin master //拉取服务器的代码
    git stash pop //合并暂存的代码
    ```

2. 服务器代码覆盖本地代码

    ```shell
    git reset --hard  //回滚到上一个版本
    git pull origin master
    ```

### 版本控制

`git log`

`git log --pretty=oneline`

`git log --oneline`

`git reflog`

`git reset --hard [部分索引值]`

### 隐藏文件

exclude

```shell
# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
# For a project mostly in C, the following would be a good set of
# exclude patterns (uncomment them if you want to use them):
# *.[oa]
# *~
.*
```

### 忽略文件

`touch .gitignore`
`.git/info/exclude``
``vi .git/info/exclude`

1. 忽略文件中的空行或以井号( #告开始的行将会被忽略。
2. 可以使用Linux通配符。例如:星号(* )代表任意多个字符,问号( ? ) 代表-一个字符 ,方括号( [abc] )代表可选字符范围,
   大括号( {string1,string.... )代表可选的字符串等。
3. 如果名称的最前面有一一个感叹号(!) , 表示例外规则,将不被忽略。
4. 如果名称的最前面是一个路径分隔符 (/ ) , 表示要忽略的文件在此目录下,而子目录中的文件不忽略。
5. 如果名称的最后面是一个路径分隔符(/) , 表示要忽略的是此目录下该名称的子目录,而非文件(默认文件或目录都忽略)。

### 删除远程文件

方法一
这里以删除 test文件夹为案例

```shell
git rm -r --cached test //--cached不会把本地的test删除
git commit -m 'delete test '
git push -u origin master
```

方法二
如果误提交的文件夹比较多，方法一也较繁琐
直接修改.gitignore文件,将不需要的文件过滤掉，然后执行命令:

```shell
git rm -r --cached .
git add .
git commit
git push -u origin master
```

> [参考博客](https://blog.csdn.net/cui130/article/details/84033966)

### 下载文件

单个文件[DownGit](https://minhaskamal.github.io/DownGit/#/home)，国内访问较慢

将仓库克隆到码云上在下载

使用迅雷

## 错误

1. 原因是因为上传时间太长了，导致ssh连接超时断开

```shell
fatal: sha1 file '<stdout>' write error: Broken pipe KiB/s
fatal: the remote end hung up unexpectedly
fatal: the remote end hung up unexpectedly

```

> [解决方案](<https://blog.csdn.net/qq_31803503/article/details/88323527>)

### push问题

#### ! [rejected] master -> master (non-fast-forward)

##### 错误

```shell
$ git push
To github.com:Lanqilu/BiliBilil-Leaderboard.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'git@github.com:Lanqilu/BiliBilil-Leaderboard.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

##### 问题

git仓库中已经有一部分代码，所以它不允许你直接把你的代码覆盖上去

##### 解决方法

利用强覆盖方式用你本地的代码替代git仓库内的内容

> 不推荐使用，需要解决冲突再提交

```shell
git push -f
```

[参考地址](https://blog.csdn.net/lujinjian605894472/article/details/8443403)

<!--hexo clean && hexo g && hexo d-->

### pull问题

#### 问题描述：本地内容有修改但要与pull

#### 解决办法

先git status暂存

再pull之后git status pop 选择修改