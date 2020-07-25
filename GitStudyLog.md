# Git Study Log

## 一  Git基础

### 1 Git本地命令

```
git help <command>
git <command> help
git <command> -h
man git-<command>
```

获取命令command的用法说明，例如git help add、git help config。

#### 1.1 常用命令

```
git init
```

将当前文件夹初始化为Git仓库，git init初始化Git仓库之后会默认生成一个主分支master，也是你所在的默认分支，基本上是实际开发中正式环境下的分支，一般情况下master分支不会轻易直接在上面操作。

```
git status
git status -s/--short
```

查看状态，默认直接在master分支，可以查看文件夹下文档所处状态。在工作目录下的每一个文件有且仅有两种状态：**已跟踪**或**未跟踪**，已跟踪的文件是指那些被纳入版本控制的文件（即Git已经知道的文件），在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能是未修改、已修改或已放入暂存区。

第二种查看状态的命令可以得到一种格式更为简洁的输出。

![image-20200724191626665](./illustration/image-20200724191626665.png)

```
git add <filename>
git add <directory>
```

将“filename”文件添加到Git仓库，但是未提交。git add后面如果是目录的话，将会递归地（添加）跟踪该目录下的所有文件。git add命令的理解为“精确地将内容添加到下一次提交中”。**运行了git add之后又作了修订的文件，需要重新运行git add把最新版本重新暂存起来**。

（git add是一个多功能的命令，以后用到再来添加）

```
git commit <filename> -m "a description of the commit information"
```

将filename文件提交到Git仓库，并添加说明信息

git add是先把改动添加到一个”暂存区“，临时保存你的改动，而git commit才是最后真正的提交。

```
git log
```

查看所有的commit记录

```
git branch
```

查看分支

```
git branch <branch_name>
git branch -d <branch_name>
```

创建一个名字为branch_name的分支。删除名字为branch_name的分支，-D为强制删除。

```
git merge <branch_name>
```

首先要切换到master分支，执行git merge branch_name，将名字为branch_name的分支合并到master分支。

```
git tag <tag_name>
```

将当前Git仓库版本（当前的代码或文件版本）打上标签tag_name，git tag可以查看历史tag记录。

#### 1.2 checkout

```
git checkout <branch_name>
```

切换到名字为branch_name的分支。

```
git checkout -b <branch_name>
```

创建并切换到名字为branch_name的分支。

```
git checkout <commit_id>
```

切换到某次commit。

```
git checkout <tag_name>
```

切换到名字为tag_name时的Git仓库版本

checkout除了有“切换”的意思，checkout还有撤销的作用，假如你现在在实现一个小功能，快写完的时候，突然需求变了，之前写的代码完全不能用了，好在你还没有将该文件git add进暂存区，这时可以用下面的命令直接将文件还原到修改之前的样子

```
git checkout <filename>
```

checkout只能撤销还没有add进缓存区的文件。

#### 1.3 stash

代码未完成之前一般不建议commit，会产生垃圾commit。当你正在一个新的分支上开发新的功能的时候，突然有一个紧急的bug需要修复，此时可以利用**stash**命令保存我们现在分支中的代码，让我们暂时切换到其他分支，修复完bug在切换回原来的分支。使用该命令的前提是没有进行commit，add代码也没关系。

```
git stash
```

将当前分支**所有没有commit**的代码先暂存起来，这时执行git status会发现当前分支非常干净，几乎看不到任何改动，之前修改的代码页看不见了，其实是stash起来了。

```
git stash list
```

查看stash记录，会查看到执行git stash命令的分支，这时就可以切换到其他分支，修复bug，结束之后，再切换回原来的分支继续之前未完成的工作。

```
git stash apply
```

回复执行git stash命令的分支。

```
git stash drop
```

把需要的代码恢复完成后，最好将暂存区的这次stash记录删除。

```
git stash pop
```

该命令不仅将stash的代码恢复回来，还自动帮你删除了这条stash记录。确保万一，可以执行git stash list查看一下是否还有该次stash记录。

git stash clear

清空所有暂存区的记录，drop是只删除一条记录，后面加上stash_id可以删除指定的stash记录，不加参数就是删除最近的一条记录，clear是清空记录。

#### 1.4 merge & rebase

rebase将两个分支先进行比较，按照一定的规则（时间？，内容？）合并，使得合并之后的代码很有逻辑，但是很难清晰的知道代码的出处。而merge则直接将另一分支的代码放到当前分支，虽然简单粗暴，但是很清楚的知道代码的来源。

##### 1.4.1 merge合并

merge是合并的意思，我们在一个featureA分支开发完一个功能，需要合并到主分支master上时，可以进行如下操作

```
git checkout master
git merge featureA
```

首先切换到master分支，然后把featureA分支合并到master分支上。

##### 1.4.2 rebase合并

rebase也是合并的意思，其用法如下

```
git checkout master
git rebase featureA
```

首先切换到master分支，然后将featureA分支合并到当前分支。

### 2 Git远程仓库命令

```
ssh-keygen -t rsa
```

生成SSH key，连续输入三个回车即可生成密钥文件id_rsa与公钥文件id_rsa.pub，找到生成的公钥，将内容添加到Github后，就可以向Github提交代码或文件等。

```
git config --global user.name "TMDWang"
git config --global user.email "z.wang@siwave.com.cn"
```

在提交代码之前要设置自己的用户名与邮箱，这些信息会出现在所有的commit记录里，执行以上代码进行设置（我自己的用户名与邮箱）。

```
git push origin master
```

将本地代码推到远程仓库origin的master分支。

```
git pull origin master
```

将远程仓库origin的master分支的**最新代码更新**到本地，一般在push之前都会先pull，这样不容易冲突。

#### 2.1 向远程仓库提交代码的两种方法

##### 2.1.1 本地关联GitHub远程仓库

本地已有一个完整的Git仓库，并且进行了很多次commit，可以利用下面的命令将本地仓库里的项目与远程仓库的项目进行关联，关联之前要保证远程仓库Github中已经有该项目（下方命令中的MyStudyLog.git），若没有，要新建一个。

```
git remote add origin git@github.com:TMDWang/MyStudyLog.git
```

给当前目录中的项目添加一个远程仓库，"origin"：远程仓库名，可自己随意取名，如果只有一个远程仓库时，一般取名origin，如果有多个远程仓库，比如Github一个，公司一个，这样的话提交到不同的远程仓库就需要指定不同的仓库名字。"git@github.com:TMDWang/MyStudyLog.git"：Github远程仓库SSH地址。给当前项目添加完远程仓库之后，就可以git push origin mastert，向origin的master分支提交代码了。

```
git remote -v
```

查看当前项目有哪些远程仓库

##### 2.1.2 Clone自己的项目

```
git clone git@github.com:TMDWang/MyStudyLog.git
```

这样就把远程仓库中的MyStudyLog项目clone到了本地，此时该项目本身已经是一个git仓库，不需要执行git init进行初始化，而且已经关联好了远程仓库，我们只需要在该目录下修改或者添加文件，然后进行commit之后，就可以git push origin master,向远程仓库提交代码了。

Git支持多种数据传输协议，http://协议、git://协议、SSH传输协议（上例所使用）。

### 3 config & alias

#### 3.1 config（Git配置）

```
git config --list
```

查看所有的配置信息

```
git config <key>
```

查看某一项的配置信息，例如git config user.name。

```
git config --global user.name "TMDWang"
git config --global user.email "z.wang@siwave.com.cn"
```

配置用户名与邮箱

```
git config --global core.editor "'D:/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
```

配置默认的编辑器，可以在安装程序时选择，也可以用上面的命令修改。

#### 3.2 alias（别名、外号）

```
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
```

将"log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"取一个别名lg。

```
git config --global alias.my_test_ssh "git@github.com:TMDWang/my_test.git"
```

将自己仓库的某个项目ssh地址（"git@github.com:TMDWang/my_test.git"）取个简短的别名my_test_ssh。

### 4 工作流程（一般情况下）

在本地项目中完成工作之后，git status可以查看修改文件后的状态，GitStudyLog.md文件被修改但是未添加到暂存区。

![image-20200724133150879](./illustration/image-20200724133150879.png)

添加到暂存区，未提交状态，此时可以用"git restore --staged filename"命令撤销暂存区的内容。

![image-20200724133613713](./illustration/image-20200724133613713.png)

提交到仓库，此时的状态为空。然后将本地同步到远程（git push origin master），之后可以进行修改再add、commit、同步......。

![image-20200724133835998](./illustration/image-20200724133835998.png)

同步到远程仓库

![image-20200724134334706](./illustration/image-20200724134334706.png)

### 5 diff 比较文件差异

```
git diff <commit_id1> <commit_id2>
```

比较两次提交之间的差异，commit_id是每次commit的SHA1值，可以根据git log看到，是长度为40的字符串。

```
git diff <branch1> <branch2>
```

比较两个分支之间的差异

```
git diff --staged
```

比较暂存区和版本库之间的差异

### 6 解决冲突

在两个分支上更改了同一文件，当合并到主分支master上时，第二次commit会提示冲突conflicts。此时需要手动解决这个冲突之后重新进行提交。

（待补充）

### 7 忽略文件



第35页，晚上添加

## 二 分支

通常我们默认都会有一个主分支叫master。

### 2.1 常用命令

```
git branch develop
```

创建一个基于当前master分支且名字为develop的新分支，此时develop分支与master主分支的内容完全一样。

```
git checkout develop
```

切换到develop分支。

```
git checkout -b develop
```

合并以上两步，新建develop分支并切到该分支下。

```
git push origin develop
```

将develop分支推送到名字为origin的远程仓库。

```
git branch
git branch -r
```

查看本地分支列表，查看远程分支列表

```
git branch -d develop
git branch -D develop
```

删除本地develop分支，强制删除本地develop分支

```
git push origin :develop
```

删除远程分支

```
git checkout develop origin/develop
```

如果远程有个分支develop，而本地没有，执行上条命令可以将远程分支迁到本地。

```
git checkout -b develop origin/develop
```

将远程分支迁到本地并切换到该分支。

### 2.2 分支管理流程Git Flow

Git Flow是一种比较成熟的分支管理流程。整个工作流程如下图所示。

![The “git flow” branching model](illustration/git-flow.png)

Github地址：https://github.com/nvie/gitflow

相关博客：http://stormzhang.com/git/2014/01/29/git-flow/









## 参考文献

[1] stormzhang,《从 0 开始学习 GitHub 系列》，

[2] Scott Chacon & Ben Straub, 《Pro Git》,