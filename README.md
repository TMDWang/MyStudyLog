##### 目录

- ./Embedded：学习嵌入式笔记，包括C语言、FreeRTOS、Gcc。
- ./Git：学习使用Git。
- ./LinuxCentosServer：参考《菜鸟》学习Linux操作系统。
- ./*/illustration：存放各目录下**.md文件中的插图。
- ./Doxygen：学习Doxygen工具使用。

##### 文件

- .gitignore：用于指示忽略当前Git仓库中哪些文件或目录的修改。

  .gitignore只能忽略那些原来没有被追踪的文件，如果 某些文件已经被纳入了版本管理中，则修改.gitignore文件是无效的。那么解决方法是先将本地缓存的删除（改变成未被追踪状态），然后再提交：

  ```shell
  git rm -r --cached .
  git add .
  git commit -m "update .gitignore"
  git push 
  ```

  注意：

  1、.gitignore文件只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。

  2、想要.gitignore起作用，必须要在这些文件不在暂存区中才可以，.gitignore文件只是忽略没有被staged（cached）文件，对于已经被staged文件，加入.gitignore文件时一点更要先从staged移除，才可以忽略。

- README.md：本文件。
- C_Program.md：主要用于记录本人（菜鸟）在使用C语言时学到的新知识、遇到的问题以及原因和解决办法、巧妙的编程技巧、常用的方法函数等。
- FreeRTOS_Study_Log.md：为本人学习FreeRTOS操作系统时的学习笔记。
- GitStudylog.md：为本人学习Git的使用时的学习笔记，主要是对文件最后参考文献的摘抄。
- visual_studio_C_user_guide.md：visual studio 2013软件创建并运行.c文件的过程，包括创建项目、添加源文件等操作。

  

