# Git Study Log

## 一  Git基础

### 1 Git本地命令

#### 1.1 git help查看帮助

```
git help <command>
git <command> help
git <command> -h
man git-<command>
```

获取命令command的用法说明，例如git help add、git help config。

#### 1.2 git init初始化仓库

```
git init
```

将当前文件夹初始化为Git仓库，git init初始化Git仓库之后会默认生成一个主分支master，也是你所在的默认分支，基本上是实际开发中正式环境下的分支，一般情况下master分支不会轻易直接在上面操作。

#### 1.3 git status查看状态

```
git status
git status -s/--short
```

查看状态，默认直接在master分支，可以查看文件夹下文档所处状态。在工作目录下的每一个文件有且仅有两种状态：**已跟踪**或**未跟踪**，已跟踪的文件是指那些被纳入版本控制的文件（即Git已经知道的文件），在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能是未修改、已修改或已放入暂存区。

第二种查看状态的命令可以得到一种格式更为简洁的输出。

![image-20200724191626665](./illustration/image-20200724191626665.png)

#### 1.4 git add将修改添加到暂存

```
git add <filename>
git add <directory>
```

将“filename”文件添加到Git仓库，但是未提交。git add后面如果是目录的话，将会递归地（添加）跟踪该目录下的所有文件。git add命令的理解为“精确地将内容添加到下一次提交中”。**运行了git add之后又作了修订的文件，需要重新运行git add把最新版本重新暂存起来**。

（git add是一个多功能的命令，以后用到再来添加）

#### 1.5 git commit提交暂存区域快照

```
git commit <filename> -m "a description of the commit information"
git commit
git commit -v
```

将filename文件提交到Git仓库，并添加说明信息。执行第二条会将所有执行过git add的文件提交，并打开默认的文本编辑器提示你输出提交说明。执行第三条可以查看更详细的内容修改提示。

在执行git commit之前，应该执行git status命令查看一下是否还有修改过的文件或者新添加的未追踪的文件没有被add进暂存区。git add是先把改动添加到一个”暂存区“，临时保存你的改动，而git commit才是最后真正的提交。

```
git commit -a
```

加上-a选项，Git会自动把所有已跟踪过的文件暂存起来一并提交，从而跳过git add步骤，这很方便，但是有时会将不需要的文件添加到提交中（我觉得这句话的意思是，没有追踪过的文件也会暂存并提交）。

#### 1.6 git mv移动文件（重命名？）

```
git mv file_from file_to
```

将文件file_from更名为file_to，与下面三行命令作用相同

```
mv file_from file_to
git rm file_from
git add file_to
```

#### 1.7git log查看提交历史

```
git log
```

不传入任何参数的默认情况下，会按照时间先后顺序查看所有的commit记录，最近的更新在最上面。这个命令会列出每个提交的SHA-1校验和、作者的名字和邮箱地址、提交时间和提交说明，如下图所示。

![image-20200726191534116](illustration/image-20200726191534116.png)

```
git log -p/--patch
git log -2
```

按补丁的格式输出每次提交所引入的差异。显示最近的两次提交。

```
git log --stat
```

查看每次提交的简略统计信息，列出所有被修改过的文件、有多少文件被修改了以及被修改过的文件的哪些行被移除或是添加了。在最后对每次提交做一个总结。

```
git log --pretty=oneline/short/full/fuller
git log --pretty=format:"%h - %an, %ar : %s"
```

使用不同于默认格式的方式展示提交历史。下图为git log --pretty=format长用选项。

![image-20200726193534192](illustration/image-20200726193534192.png)



下图为git log的常用选项

![image-20200726193844552](illustration/image-20200726193844552.png)



#### 待修改

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

#### 1.8 git checkout

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

#### 1.9 git stash

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

#### 1.10 git merge & git rebase

rebase将两个分支先进行比较，按照一定的规则（时间？，内容？）合并，使得合并之后的代码很有逻辑，但是很难清晰的知道代码的出处。而merge则直接将另一分支的代码放到当前分支，虽然简单粗暴，但是很清楚的知道代码的来源。

##### 1.10.1 git merge合并

merge是合并的意思，我们在一个featureA分支开发完一个功能，需要合并到主分支master上时，可以进行如下操作

```
git checkout master
git merge featureA
```

首先切换到master分支，然后把featureA分支合并到master分支上。

##### 1.10.2 git rebase合并

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
git diff
```

不加参数，可以查看尚未暂存的文件更新了哪些部分。此命令比较的是工作目录中当前文件和暂存区域快照之间的差异，也就是修改之后还没有暂存起来的内容变化。下图为执行该命令后的输出。

![image-20200726011820244](illustration/image-20200726011820244.png)

其中前面带有+号的是增加的内容，带有-号的是删除的内容。

```
git diff --staged/--cached
```

查看已暂存的将要添加到下次提交的内容，这条命令可以对比已暂存文件和最后一次提交的文件差异。

```
git diff <commit_id1> <commit_id2>
```

比较两次提交之间的差异，commit_id是每次commit的SHA1值，可以根据git log看到，是长度为40的字符串。

```
git diff <branch1> <branch2>
```

比较两个分支之间的差异

比较文件差异也可以使用图形化的工具或外部diff工具来比较差异。可以使用git difftool命令来调用emerge或vimdiff等软件**输出**diff的分析结果。使用下面的命令可以查看你系统中支持哪些Git Diff插件。（**配置difftool**，待补充）

```
git difftool --tool-help
```

![image-20200726013420728](illustration/image-20200726013420728.png)

### 6 解决冲突

在两个分支上更改了同一文件，当合并到主分支master上时，第二次commit会提示冲突conflicts。此时需要手动解决这个冲突之后重新进行提交。

（待补充）

### 7 忽略文件

一般总会有些文件无需纳入Git管理，也不希望它们总出现在未跟踪文件列表，通常都是一些自动生成的文件，比如日志、编译过程中创建的临时文件等。在这种情况下，我们可以创建一个名为.gitignore的文件，列出要忽略的文件。要养成一开始就为你的新仓库设置好.gitignore文件的习惯，以免将来误提交无用的文件。最简单的情况下，一个仓库可能只有根目录下一个.gitignore文件，它递归应用到整个仓库中。然而，子目录下也可以有额外的.gitignore文件，文件中的规则只作用于它所在的目录中。语法规则示例如下：

```
*.[oa]			# 忽略所有以.o、.a结尾的文件
!lib.a			# 但跟踪所有的lib.a文件，即使前面忽略了.a文件
*~				# 忽略所有名字以波浪符（~）结尾的文件
/TODO			# 只忽略当前目录下的TODO文件，不忽略其他目录下的
build/			# 忽略任何目录下名为build的文件夹
doc/*.txt		# 忽略doc/notes.txt，但不忽略doc/server/arch.txt
doc/**/*.pdf	# 忽略doc/目录及其所有子目录下的.pdf文件
```

文件.gitignore的格式如下：

- 所有空行或者以#开头的行都会被Git忽略。
- 可以使用标准的glob模式匹配，它会递归地应用在整个工作区。
- 匹配模式可以以（/）开头防止递归。
- 匹配模式可以以（/）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（！）取反。

所谓的glob模式是指shell所使用的简化了的**正则表达式**。十分详细的针对数十种项目及语言的.gitignore文件列表：https://github.com/github/gitignore。

简单的通配符列表（待补充）



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

[1] Stormzhang,《从 0 开始学习 GitHub 系列》.

[2] Scott Chacon & Ben Straub, 《Pro Git》.