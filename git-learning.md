## git简介 
Git是由Linux的创始人Linus Torvalds在2005年编写完成，不断发展完善的主流的分支式版本控制系统，可以有效、快速的处理任何或小或大的项目。本篇主要介绍git常用的操作，方面大家入门，具体的介绍就不展开说明了，详情见：[Git官网链接](https://git-scm.com/)。下面是本人在操作过程中理解到的git与svn的主要不同点，后面就直接展开操作了。
+ git属于分支式版本控制系统，svn属于集中式版本控制系统
+ git比较安全，如果版本库出现问题，更容易修复
+ svn只有一个中央版本库，git可以有无限个版本库
+ git分支的新建不会影响其他成员
+ git占用空间小，速度快 

<!--more-->
## git安装和配置 

### 安装 
git可以在多个平台安装，下面列出三种：
+ Linux：用系统提供的包管理工具安装  

+ Mac：使用图形化的 Git 安装工具或通过MacPorts安装  

+ Windows：下载 exe 安装文件并运行

### 配置
安装完成后，打开“Git Bash"，弹出命令窗口进行配置：

#### 基本配置  
第一个要配置的是个人的用户名称和电子邮件地址。这两条配置很重要，每次 Git 提交时都会引用这两条信息，说明是谁提交了更新，所以会随更新内容一起被永久纳入历史记录：

``` shell   
$ git config --global user.name "Yuan"  
$ git config --global user.email xxx@qq.com
```    

#### 文本编辑器配置
接下来要设置的是默认使用的文本编辑器。Git需要输入一些额外消息的时候，会自动调用一个外部文本编辑器。比如使用vi： 

``` shell   
$ git config --global core.editor vi 
``` 

#### 差异分析工具配置  
还有一个比较常用的是，在解决合并冲突时使用哪种差异分析工具。比如使用 vimdiff：

``` shell   
$ git config --global merge.tool vimdiff 
``` 


## git基本操作 

### 取得仓库文件
有两种取得 Git 项目仓库的方法。第一种是在现存的目录下，通过导入所有文件来创建新的 Git 仓库。第二种是从已有的 Git 仓库克隆出一个新的镜像仓库来。

  功能 | 命令
---|--- 
初始化新仓库 | $ **git init**  
从远程仓库复制出新仓库 | $ **git clone [url]**
     
### 记录仓库的更新记录
文件工作目录下的每一个文件都不外乎这两种状态：已跟踪或未跟踪。 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。  

功能 | 命令
---|---
检查当前文件状态 | $ **git status**
跟踪新文件 | $ **git add [name]** 
比较修改后的差异|$ **git diff** 
暂存已修改文件|$ **git add [name]**
提交更新|$ **git commit -m [mess]**
删除文件|$ **git rm [name]**
移动文件|$ **git mv [source] [desti]** 
重命名文件|$ **git mv [oldname] [newname]** 

### 查看提交历史
在提交了若干更新，又或者克隆了某个项目之后，想要回顾提交历史。完成这个任务最简单而又有效的工具是 $**git log** 命令,也可以使用简化的$**git shortlog**命令查看简要信息。默认不用任何参数的话，git log 会按提交时间列出所有的更新，最近的更新排在最上面。    

#### git log的常用选项

选项 | 说明
---|---
-p | 显示每次更新之间的差异。
--stat| 列出修改的文件和每个文件中修改了多少行。
--shortstat|列出修改多少行，不显示哪些文件。
--name-only|仅在提交信息后显示已修改的文件。

#### git log的限制输出

选项 | 说明
---|---
-(n)| 仅显示最近的 n 条提交
--since="xxxx-xx-xx", --after="xxxx-xx-xx"| 仅显示指定时间之后的提交。
--until="xxxx-xx-xx", --before="xxxx-xx-xx"|仅显示指定时间之前的提交。
--author|仅显示指定作者相关的提交。
--committer|仅显示指定提交者相关的提交。
--grep|仅显示含指定关键字的提交

### 撤销操作
撤销在不同场景运用的命令有所不同：

操作| 命令
---|---
取消暂存的文件|$ **git reset HEAD [file]**
撤消对文件的修改 |$ **git checkout -- [file]**

## git相关原理

### 先了解git文件的四种状态及流程
git工作目录下文件有的四种状态，分别为：未跟踪、未修改、已修改、已暂存，基本操作流程包括：
1. 对文件进行修改和处理
2. 将文件保存到暂存区
3. 将暂存区的文件提交 

具体流程如图所示：

{% asset_img git-learning2.png git-flow %}

### 仓库的目录结构  
在操作系统中，仓库就是一个文件夹，在创建时生成，名为.git，git版本控制所需要的所有文件都在这个文件夹中。  核心文件有：config文件、objects文件夹、HEAD文件、index文件以及refs文件夹：
- config文件：该文件主要记录针对该项目的一些配置信息，仓库的配置文件，很多配置信息都在这里。
+ objects文件夹：该文件夹主要包含git对象。Git中的文件和一些操作都会以git对象来保存，git对象分为BLOB、tree和commit三种类型。  
+ HEAD文件：该文件指明了git branch（即当前分支）的结果，比如当前分支是master，则该文件就会指向master，但是并不是存储一个master字符串，而是分支在refs中的表示，相当于指针。例如当前在master分支，则文件内容为ref: refs/heads/master，若新建并切换分支到master1，则HEAD文件的内容为ref: refs/heads/master1。
+ index文件：该文件保存了暂存区域的信息。该文件某种程度就是缓冲区，内容包括它指向的文件的时间戳、文件名、sha1值等
+ refs文件夹：该文件夹存储引用文件。其中heads文件夹存储本地每一个分支最近一次commit的sha-1值，每个分支一个文件；remotes文件夹则记录最后一次和每一个远程仓库的通信，Git会把最后一次推送到这个remote的每个分支的值都记录在这个文件夹中 

## git进阶操作
### Git分支操作
Git处理分支的方式轻量，创建新分支这一操作能在瞬间完成，并且在不同分支之间的切换操作也是一样便捷。 

#### 分支相关命令

操作 | 命令
---|---
查看分支 | $ **git branch**
创建分支 | $ **git branch [name]**
切换分支 | $ **git checkout [name]**
创建并切换分支| $ **git checkout -b [name]**
合并某分支到当前分支|$ **git merge [name]**
删除分支|$ **git branch -d [name]** 

#### 解决分支冲突
当两个分支对同一个文件进行更改的时候，就会产生分支冲突。  
解决方法：当发生分支合并冲突的时候，使用**git status**命令，版本库会告诉我们哪个文件产生了冲突，然后手动将两个文件内容修改一样，再添加到暂存区并提交即可解决分支合并冲突。



### GitHub操作

github是一个基于git的代码托管平台，可以实现 Git 托管、问题追踪、代码审查以及其它事情。高效地使用GitHub，是我们需要学习的。

#### 注册和连接
注册和连接就不详细叙述了，需要提一下的是github支持http和https两种连接方式，具体可直接参考官方提供的操作：
[GitHub官网链接](https://github.com)
[参阅 GitHub 的 SSH 密钥指南](https://help.github.com/articles/generating-ssh-keys)  

#### 本地与GitHub交互
通过 Git 分享你的代码或者与其他开发人员合作，你就需要将数据放到一台其他开发人员能够连接的服务器上。用Github作为远程仓库，是一个很好的选择。一般可以这样理解：**pull=fetch+merge** ，远程仓库相关命令如下：

操作 | 命令
---|---
查看远程仓库名称 | $ **git remote**
查看远程仓库地址等详细信息 | $ **git remote -v**
添加远程仓库 | $ **git remote add [shortname] [url]**
从远程仓库中拉取到本地| $ **git fetch [remote-name]**
推送到远程仓库|$ **git push [shortname] [branch-name]**
强制push到远程仓库|**$ git push -u [shortname] [branch-name] -f**
将远程存储库中的更改合并到本地|$ **git pull [remote-name] [remote-branch]:[local-branch]**
查看远程仓库|$ **git remote show [remote-name]**
远程仓库的重命名| $ **git remote rename [old name] [new name]**
远程仓库的移除| $ **git remote rm [remote-name]**

## git扩展操作
### 打标签
像其他版本控制系统一样，Git 可以给历史中的某一个提交打上标签，以示重要。
git标签分为两种类型：轻量标签和附注标签。轻量标签是指向提交对象的引用，附注标签则是仓库中的一个独立对象。建议使用附注标签。

操作 | 命令
---|---
新建轻量标签|$ **git tag [tagname]**
新建附注标签|$ **git tag -a [tagname]-m [message]**
查看所有标签名|$ **git tag**
查看某个轻量标签的提交信息|$ **git show [tagname]**  
显示离当前提交最近的标签|$ **git describe** 
查看某个附注标签的标签信息和提交信息|$ **git show [tagname]**
对过去的提交打标签|$ **git tag [tagname] [commitid]**
git push不会提交标签到远程仓库，需手动提交标签|$ **git push [shortname] [tagname]**
提交所有标签|$ **git push [shortname] --tags**
删除本地仓库标签|$ **git tag -d [tagname]**
删除远程仓库标签|$ **git push [shortname] :refs/tags/[tagname]** 

### 起别名
为复杂的命令起别名，可以不用每次都输入完整的命令，只需输入别名即可。删除配置的别名，在文件.git/config中，直接把对应行删除。

操作 | 命令
---|---
为当前仓库配置别名|$ **git config alias.[newname] [oldname]**
为当前用户下所有仓库配置别名|$ **git config --global alias.[newname] [oldname]**  

### 储藏  
“储藏”可以获取工作目录的中间状态——也就是修改过的被追踪的文件和暂存的变更，并将它保存到一个未完结变更的堆栈中，随时可以重新应用。  

操作 | 命令
---|---
建立新的储存 | $ **git stash**
查看现有的储存 | $ **git stash list**
取出最近的储存 | $ **git stash apply**
取出更早的储存| $ **git stash apply [stashname]**
移除堆栈中的某个储存|$ **git stash drop [stashname]**
取出最近的储存并立刻将其从堆栈中移走|**git stash pop**
取出更早的储存并立刻将其从堆栈中移走|**git stash pop [stashname]**  
 

## git参考资料

1. [官方文档](https://git-scm.com/docs)  ：   https://git-scm.com/docs
2. [中文推荐文档](https://git-scm.com/book)  ：  https://git-scm.com/book