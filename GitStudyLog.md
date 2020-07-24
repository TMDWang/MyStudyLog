# Git Study Log

## 一  常用命令

### 1 Git本地命令

```
git init
```

将当前文件夹初始化为Git仓库，git init初始化Git仓库之后会默认生成一个主分支master，也是你所在的默认分支，基本上是实际开发中正式环境下的分支，一般情况下master分支不会轻易直接在上面操作。

```
git status
```

查看状态，默认直接在master分支，可以查看文件夹下文档所处状态（待补充）

```
git add "filename"
```

将“filename”文件添加到Git仓库，但是未提交

```
git commit "filename" -m "a description of the commit information"
```

将“filename”文件提交到Git仓库，并添加说明信息

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
git branch "branch_name"	
git branch -d "branch_name"
```

创建一个名字为"branch_name"的分支。删除名字为"branch_name"的分支，-D为强制删除。

```
git checkout "branch_name"
```

切换到名字为"branch_name"的分支。

```
git checkout -b "branch_name"
```

创建并切换到名字为"branch_name"的分支。

```
git merge "branch_name"
```

首先要切换到master分支，执行git merge "branch_name"，将名字为"branch_name"的分支合并到master分支。

```
git tag "tag_name"
```

将当前Git仓库版本（当前的代码或文件版本）打上标签"tag_name"，git tag可以查看历史tag记录。

```
git checkout "tag_name"
```

切换到名字为"tag_name"时的Git仓库版本

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

查看当前项目由哪些远程仓库

##### 2.1.2 Clone自己的项目

```
git clone git@github.com:TMDWang/MyStudyLog.git
```

这样就把远程仓库中的MyStudyLog项目clone到了本地，此时该项目本身已经是一个git仓库，不需要执行git init进行初始化，而且已经关联好了远程仓库，我们只需要在该目录下修改或者添加文件，然后进行commit之后，就可以git push origin master,向远程仓库提交代码了。

### 3 config & alias

#### 3.1 config

```
git config --global user.name "TMDWang"
git config --global user.email "z.wang@siwave.com.cn"
```

配置用户名与邮箱

```
git config --global core.editor "'D:/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
```

配置默认的编辑器，可以在安装程序时选择，也可以用上面的命令修改。

#### 3.2 alias

```
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
```

将"log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"取一个别名lg。

```
git config --global alias.my_test_ssh "git@github.com:TMDWang/my_test.git"
```

将自己某个仓库"git@github.com:TMDWang/my_test.git"取别名my_test_ssh。

## 二 分支



