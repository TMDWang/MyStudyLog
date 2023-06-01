### CMake菜鸟日记

CMakeLists.txt的语法比较简单（不是我说的），由**命令**、**注释**和**空格**组成，其中**命令是不区分大小写**的。**符号#后面的内容被认为是注释**。命令由命令名称、小括号和参数组成。参数之间使用空格进行间隔。

在Linux平台下使用CMake生成Makefile并编译的流程如下：

- 编写CMake配置文件CMakeLists.txt。
- 执行命令cmake PATH或者ccmake PATH生成Makefile。其中，PATH是CMakelists.txt所在的目录。
- 使用make命令进行编译

#### 1 命令

##### 1.1 cmake_minimum_required()

生成链接库

##### 1.2 project()



##### 1.3 add_executable()



##### 1.4 aux_source_directory()



##### 1.5 set()



##### 1.6 add_subdirectory()



##### 1.7 target_link_libraries()



##### 1.8 add_library()



##### 1.9 message()



##### 1.10 configure_file()



##### 1.11 option()



##### 1.12 include_directories()



##### 1.13 target_sources()



1.14 

