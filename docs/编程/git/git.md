
# 概述

## 版本控制器方式

-  集中式版本控制工具 
   -  集中式版本控制工具，版本库是集中存放在中央服务器中，team中每个人work时从中央服务器下载代码，必须联网才能工作，局域网或互联网。个人修改后提交到中央版本库。 
   -  例：SVN, CVS 
-  分布式版本控制工具 
   - 分布式版本控制系统没有”中央服务器“，每个人的电脑上都是一个完整的版本库，工作时无需联网。多人协作时只需将各自修改推送给对方，就能互相看到对方的修改
   - 例：Git


## 工作流程

![](https://raw.githubusercontent.com/lonely06/images/main/md/image-20220510202550396.png#crop=0&crop=0&crop=1&crop=1&id=WStrr&originHeight=447&originWidth=1297&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## 安装

-  下载地址：[https://git-scm.com/downloads](https://git-scm.com/downloads) 
-  安装后会有两个软件 
   - Git GUI：Git提供的图形界面工具
   - Git Bash：Git提供的命令行工具
-  安装完成后需设置用户名和email地址。因为每次Git提交都会使用该用户信息 


## Git环境配置


### 基本配置

1.  打开Git Bash 
2.  设置用户信息 
```bash
git config --global user.name "..."
git config --global user.email "..."
```
 

3.  查看配置信息 
```bash
git config --list
git config --global user.name
git config --global user.email
```
 


### 为常用指令配置别名(可选)

1.  打开gitBash，执行`touch ~/.bashrc` 
2.  在.bashrc文件中输入： 
```shell
#用于输出git提交日志
alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'
```
 


### 解决GitBash乱码问题

1.  打开gitBash，执行`git config --global core.quotepath false` 
2.  在${git_home}/etc/bash.bashrc文件中最后加入 
```shell
# 解决GitBash乱码问题
export LANG="zh_CN.UTF-8"
export LC_ALL="zh_CN.UTF-8"
```
 


# 本地仓库


## 获取本地仓库

1.  在任意位置创建文件夹，作为本地git仓库 
2.  打开gitBrsh窗口，进入此文件夹 
3.  执行命令`git init` 
4.  如果创建成功后可在文件夹下看到隐藏的.git目录 


## 工作区、暂存区、版本库

**版本库**：前面看到的.git隐藏文件夹就是版本库，版本库中存储了很多配置信息、日志信息和文件版本信息等

**工作区**：包含.git文件夹的目录就是工作区，也称为工作目录，主要用于存放开发的代码

**暂存区**：.git文件夹中有很多文件，其中有一个index文件就是暂存区，也可以叫做stage。暂存区是一个临时保存修改文件的地方

![](https://raw.githubusercontent.com/lonely06/images/main/md/image-20220510142658980.png#crop=0&crop=0&crop=1&crop=1&id=kahtF&originHeight=386&originWidth=920&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


## Git工作区中文件的状态

Git工作区中的文件存在两种状态：

-  untracked 未跟踪（未被纳入版本控制） 
-  tracked 已跟踪（被纳入版本控制）<br />     1）Unmodified 未修改状态<br />     2）Modified 已修改状态<br />     3）Staged 已暂存状态 


## 常用操作


### 查看修改状态

- 作用：查看修改的状态(暂存区、工作区)
- 命令：`git status`


### 添加工作区到暂存区

- 作用：添加工作区一个或多个文件的修改到暂存区
- 命令：`git add 单个文件名|通配符` 
   - 将所有修改加入暂存区`git add .`


### 提交暂存区到本地仓库

- 作用：提交暂存区内容到本地仓库的当前分支
- 命令：`git commit -m '注释内容' [文件名]`


### 查看提交日志

- 作用：查看提交记录
- 命令：`git log [option]`或`git-log`(在常用指令中进行了配置) 
   - options 
      - `--all` 显示所有分支
      - `--pretty=oneline` 将提交信息显示为一行
      - `--abbrev-commit` 使输出的commitID更简短
      - `--graph` 以图的形式显示


### 版本回退

- 作用：版本切换
- 命令：`git reset --hard commitID` 
   - `commitID`可以使用`git-log`或`git log`指令查看
- 查看已删除的记录 
   - `git reflog`
   - 该指令可以看到已经删除的提交记录


### 添加文件至忽略列表

当有无需纳入Git管理的文件时。可以在工作目录创建一个名为`.gitignore`的文件(固定文件名)。

```shell
# no .a files
*.a
# but do track lib.a, even though you're ignoring .a files above
!lib.a
# only ignore the TODO file in the current directory, not subdir/TODO
/TODO
# ignore all files in the build/ directory
build/
# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt
# ignore all .pdf files in the doc/ directory
doc/**/*.pdf
```


# 远程仓库


## 常用的托管服务[远程仓库]

- GitHub 
   - 是一个面向开源及私有软件项目的托管平台，因为只支持Git作为唯一的版本库格式进行托管
- Gitee 
   - 是国内的一个代码托管平台
- GitLab 
   - 是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的web服务,一般用于在企业、学校等内部网络搭建git私服。


## 配置ssh公钥

- 生成ssh公钥 
   - `ssh-keygen -t rsa`
   - 不断回车 
      - 如果公钥存在，则自动创建
- 在github/gitee中设置账户公钥 
   - 获取公钥 
      - `cat ~/.ssh/id_rsa.pub`
- 验证是否配置 
   - `ssh -T git@gitee.com`
   - `ssh -T git@github.com`


## 操作远程仓库

- 注意：切换分支前先提交本地的修改


### 查看远程仓库

- 命令：`git remote [-v]`


### 添加远程仓库

-  此操作先初始化本地仓库，然后与已创建的远程仓库进行对接。可关联多个远程仓库 
-  命令：`git remote add <远端名称> <仓库路径>` 
   -  远端名称，默认是origin，取决于远端服务器设置 
   -  仓库路径，从远端服务器获取此URL 
   -  例： 
```shell
git remote add origin git@gitee.com:lonely06/test.git
```
 


### 推送到远程仓库

- 命令：`git push [-f] [--set-upstream] [远端名称 [本地分支名]:[远端分支名]]` 
   - 如果远程分支名和本地分支名称相同，则可以只写本地分支名 
      - `git push origin master`
   - `-f`表示强制覆盖
   - `--set-upstream`推送到远端的同时并且建立起和远端分支的关联关系 
      - `git push --set-upstream origin master`
   - 如果当前分支和远端分支关联，则可以省略分支名和远端名 
      - `git push` 将master分支推送到已关联的远端分支


### 查看本地分支和远端分支的关联关系

- 命令：`git branch -vv`


### 从远端仓库克隆

- 命令：`git clone <仓库路径> [本地目录]` 
   - 本地目录可以省略，会自动生成一个目录


### 从远程仓库中抓取和拉取

- 抓取命令：`git fetch [remote name] [branch name]` 
   - 抓取命令就是将仓库里的更新都抓取到本地，不会进行合并
   - 如果不指定远端名称和分支名，则抓取所有分支
- 拉取命令：`git pull [remote name] [branch name]` 
   -  拉取命令就是将远端仓库的修改拉到本地并自动进行合并，等同于fetch+merge 
   -  如果不指定远端名称和分支名，则抓取所有并更新当前分支 
   -  注意：如果当前本地仓库不是从远程仓库克隆，而是本地创建的仓库，并且仓库中存在文件，此时再从远程仓库拉取文件的时候会报错（fatal: refusing to merge unrelated histories ）<br />解决此问题可以在git pull命令后加入参数--allow-unrelated-histories 


# 分支

所有的版本控制系统都以某种形式支持分支。使用分支意味着你可以把你的工作从开发主线上分离开来进行重大的Bug修改、开发新的功能，以免影响开发主线。


## 查看本地分支

-  命令：`git branch`	列出所有本地分支<br />     `git branch -r` 列出所有远程分支<br />     `git branch -a` 列出所有本地分支和远程分支 


## 创建本地分支

- 命令：`git branch 分支名`


## 切换分支

- 命令：`git checkout 分支名`
- 切换到一个不存在的分支(创建并切换) 
   - 命令：`git checkout -b 分支名`


## 推送至远程仓库分支

- 命令：`git push 远程仓库简称 分支命令`


## 合并分支

-  一个分支上的提交可以合并到另一个分支 
-  命令：`git merge 分支名` 


## 删除分支

- 命令： 
   - `git branch -d 分支名` 删除分支时，需要作各种检查
   - `git branch -D 分支名` 不做任何检查，强制删除
- 注意 
   - 不能删除当前分支，只能删除其他分支


## 解决冲突

两个分支上对文件的修改可能会存在冲突，例如同时修改了同一个文件的同一行，这时就需要手动解决冲突。

- 步骤： 
   1. 处理文件中冲突的地方
   2. 将解决完冲突的文件加入暂存区(add)
   3. 提交到仓库(commit)


## 开发中分支使用原则与流程

-  分类： 
   - master(生产)分支 
      - 线上分支，主分支，作为线上运行的应用对应的分支
   - develop(开发)分支 
      - 从master创建的分支，作为主要的开发分支，开发阶段完成后，需要合并到master分支，准备上线
   - feature/xxxx分支 
      - 从develop创建的分支，一般是同期并行开发，但不同期上线时创建的分支，分支上的研发任务完成后合并到develop分支
   - hotfix/xxxx分支 
      - 从master派生的分支，一般作为线上bug修复使用，修复完成后需要合并到master、test、develop分支
   - 其他分支：test分支(用于代码测试)、pre分支(预上线分支)等


# 标签


## 查看标签

`git tag`


## 创建标签

`git tag 标签名`


## 将标签推送到远程仓库

`git push 远程仓库简称 标签名`


## 检出标签

`git checkout -b 分支名 标签名`

检出标签时需要新建一个分支来指向某个标签
