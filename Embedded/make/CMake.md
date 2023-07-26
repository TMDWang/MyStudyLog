### CMake菜鸟日记

CMakeLists.txt的语法比较简单（不是我说的），由**命令**、**注释**和**空格**组成，其中**命令是不区分大小写**的。**符号#后面的内容被认为是注释**。命令由命令名称、小括号和参数组成。参数之间使用空格进行间隔。

在Linux平台下使用CMake生成Makefile并编译的流程如下：

- 编写CMake配置文件CMakeLists.txt。
- 执行命令cmake PATH或者ccmake PATH生成Makefile。其中，PATH是CMakelists.txt所在的目录。
- 使用make命令进行编译

#### 0 默认变量

```cmake
# 查看cmake默认变量
cmake --help-variable-list
```

- CMAKE_SOURCE_DIR
- CMAKE_BINARY_DIR
- CMAKE_COMMAND
- CMAKE_BUILD_TYPE
- CMAKE_prefix
- CMAKE_PROJECT_NAME
- $<CONFIG>

#### 1 命令

##### 1.1 cmake_minimum_required()

指定CMake的最小~最大版本，一般只需指定最小版本。

##### 1.2 project()

指定项目名称及版本号，初始化项目相关变量。会自动创建两个变量，PROJECT_SOURCE_DIR和PROJECT_NAME。

$(PROJECT_SOURCE_DIR)：本CMakeLists.txt所在的文件夹路径

$(PROJECT_NAME)：本CMakeLists.txt的project名称

##### 1.3 add_executable()

```cmake
# 第一种：Normal Executables
add_executable(<name> [WIN32] [MACOSX_BUNDLE]
			   [EXCLUDE_FROM_ALL]
			   [source1] [source2 ...])
			   
# 第二种：Imported Executables
add_executable(<name> IMPORTED [GLOBAL])

# 第三种：Alias Executables
add_executable(<name> ALIAS <target>)
```

使用指定的源文件来生成目标可执行文件。具体分为三类：普通、导入、别名。

其中<name>是可执行文件的名称，在cmake工程中必须唯一。WIN32用于在windows下创建一个以WinMain为入口的可执行文件。MACOSX_BUNDLE用于mac系统或者IOS系统下创建一个GUI可执行应用程序。

##### 1.4 aux_source_directory()



##### 1.5 set()

```cmake
# 为一个变量赋值
set(VAR "value")
# 设置多个变量
set(VAR1 "value1" VAR2 "value2")
# 设置不存在的变量
set(VAR "value" CACHE STRING "docstring")
# 设置缓存变量，缓存变量会记录在CMakeCache.txt文件中
# FORCE参数会强制重新设置变量，即使它已经存在。
set(VAR "value" CACHE STRING "docstring" FORCE)
```

使用set()函数定义的变量能在整个项目中。

```cmake
set(PROJECT_NAME "my project")
project(${PROJECT_NAME})
add_executable(${PROJECT_NAME} main.c)
```

set()函数是在CMake中定义和管理变量的重要函数。

##### 1.6 add_subdirectory()

```cmake
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```

将子目录添加到构建系统中。source_dir指定一个目录其中存放CMakeLists.txt文件和代码文件。binary_dir指定的目录存放输出文件，如果没有指定则使用source_dir

##### 1.7 target_link_libraries()

```cmake
target_link_libraries(<target>
					  <PRIVATE|PUBLIC|INTERFACE> <item>...
					  [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)
```

为目标文件链接依赖的库。PUBLIC修饰的库或目标会被链接，并成为链接接口的一部分。PRIVATE修饰的目标或库会被链接，但不是链接接口的一部分。INTERFACE修饰的库会追加到链接接口中，但不会用来链接目标文件<target>。

##### 1.8 add_library()

```cmake
add_library(<name> [STATIC|SHARED|MODULE] [EXCLUDE_FROM_ALL] [<source>...])
```

add_library根据源码来生成一个库供他人使用。<name>是个逻辑名称，在项目中必须唯一。完整的库名依赖于具体构建方式（可能为lib<name>.a or <name>.lib）。

STATIC指静态库，是目标文件的归档文件，在链接其他目标时使用；SHARED指动态库，在运行时会被加载；MODULE指在运行期通过类似于dlopen的函数动态加载。默认状态下，库文件将会在源文件目录树的构建目录树的位置被创建，该命令也会在这里被调用。

source表示源文件。

##### 1.9 message()

message()相当于C语言中的printf函数，可以将变量的值显示到终端。因此我们可以使用message命令查看变量值是否正确。但是，message命令要比printf函数的功能强大，该命令可以终止编译系统的构建。

```cmake
message([<mode>] "message text" ...)
```

mode的值包括FATAL_ERROR、WARNING、AUTHOR_WARNING、STATUS 、VERBOSE等。

FATAL_ERROR：产生Cmake Error，会停止编译系统的构建过程

STATUS：最常用的命令，常用于查看变量值，类似于编程语言中的DEBUG级别信息。

"message text"为要显示在终端的内容。

```cmake
message()  # 直接添加打印的内容和变量即可，不需要双引号
message("PROJECT_SOURCE_DIR = ${PROJECT_SOURCE_DIR}")
```

##### 1.10 configure_file()



##### 1.11 option()



##### 1.12 include_directories()

```cmake
include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])
```

将指定目录添加到编译器的头文件搜索路径之下，指定目录被解释为当前源码路径的相对路径。[AFTER|BEFORE]定义了追加指定目录的方式在头还是尾。[SYSTEM]告诉编译器在一些平台上指定目录被当作系统头文件目录。

##### 1.13 target_sources()



##### 1.14 include()

```cmake
include(<file|module> [OPTIONAL] [RESULT_VARIABLE <VAR>]
		[NO_POLICY_SCOPE])
```

从指定的文件加载、运行CMake代码。如果指定文件，则直接处理。如果指定module，则寻找module.cmake文件，首先在${CMAKE_MODULE_PATH}中寻找，然后在CMake的module目录中查找。

##### 1.15 target_include_directories()

```cmake
target_include_directories(<target> [SYSTEM] [AFTER|BEFORE]
						   <INTERFACE|PUBLIC|PRIVATE> [items1 ...]
						   [<INTERFACE|PUBLIC|PRIVATE> [items2 ...] ...])
```

在编译目标文件<target>时指定头文件。<target>必须是通过add_executable()或add_library()创建，且不能是ALIAS目标。<INTERFACE|PUBLIC|PRIVATE>修饰其紧跟参数items的作用范围。

##### 1.16 link_directories()

```cmake
link_directories([AFTER|BEFORE] directory1 [directory2 ...])
```

为链接器添加库的搜索路径，此命令调用之后生成的目标才能生效。link_directories()要放在add_executable()之前。

##### 1.17 fine_package()

```cmake
## 两种模式
# mode1：Module，此模式需要访问ind<PackageName>.cmake文件
find_package(<PackageName> [version] [EXACT] [QUIET] [MODULE]
             [REQUIRED] [[COMPONENTS] [components ...]]
             [OPEIONAL_COMPONENTS components ...]
             [NO_POLICY_SCOPE])

# mode2：Config，此模式需要访问<LowercasePackageName>-config.cmake or <PackageName>Config.cmake
find_package(<PackageName> [version] [EXACT] [QUIET]
			 [REQUIRED] [[COMPONENTS] [components ...]]
			 [OPTIONAL_COMPONENTS components ...]
			 [CONFIG|NO_MODULE]
			 [NO_POLICY_SCOPE]
			 [NAMES name1 [name2 ...]]
			 [CONFIGS config1 [config2 ...]]
			 [HINTS path1 [path2 ...]]
			 [PATHS path1 [path2 ...]]
			 [PATH_SUFFIXES suffix1 [suffix2 ...]]
			 [NO_DEFAULT_PATH]
			 [NO_PACKAGE_ROOT_PATH]
			 [NO_CMAKE_PATH]
			 [NO_CMAKE_ENVIRONMENT_PATH]
			 [NO_SYSTEM_ENVIRONMENT_PATH]
			 [NO_CMAKE_PACKAGE_REGISTRY]
			 [NO_CMAKE_BUILDS_PATH]
			 [NO_CMAKE_SYSTEM_PATH]
			 [NO_CMAKE_SYSTEM_PACKAGE_REGISTRY]
			 [CMAKE_FIND_ROOT_PATH_BOTH |
			  ONLY_CMAKE_FIND_ROOT_PATH |
			  NO_CMAKE_FINE_ROOT_PATH])
```

find_package一般用于加载外部库到项目中，并且会加载库的细节信息。如上find_package有两种模式：Module和Config。

##### 1.18 add_custom_command()



##### 1.19 execute_process()

```cmake
execute_process(COMMAND <cmd1> [<arguments>]
				[COMMAND <cmd2> [<arguments>]] ...
				[WORKING_DIRECTORY <directory>]
				[TIMEOUT <seconds>]
				[RESULT_VARIABLE <variable>]
				[RESULTS_VARIABLE <variable>]
				[OUTPUT_VARIABLE <variable>]
				[ERROR_VARIABLE <variable>]
				[INPUT_FILE <file>]
				[OUTPUT_FILE <file>]
				[ERROR_FILE <file>]
				[OUTPUT_QUIET]
				[ERROR_QUIET]
				[COMMAND_ECHO <where>]
				[OUTPUT_STRIP_TRAILING_WHITESPACE]
				[ERROR_STRIP_TRAILING_WHITESPACE]
				[ENCODING <name>]
				[ECHO_OUTPUT_VARIABLE]
				[ECHO_ERROR_VARIABLE]
				[COMMAND_ERROR_IS_FATAL <ANY|LAST>])
```

运行一个或多个命令的给定序列。

命令作为管道并发执行，每个进程的标准输出通过管道传输到下一个进程的标准输入。所有进程都是用一个标准错误管道。

- COMMAND

一个子进程命令行

CMake直接使用操作系统api执行子进程。所有参数都被逐字传递给子进程。没有使用中间shell，所以像>这样的shell操作符被视为普通参数。（使用INPUT\_\**、OUTPUT\_\**和ERROR\_\*选项重定向stdin、stdout和stderr）

如果需要连续执行多个命令，请使用多个execute_process()调用，并使用单个COMMAND参数。

- WORKING_DIRECTORY

命名目录将被设置为子进程的当前工作目录

- TIMEOUT

在指定的秒数之后，所有未完成的子进程将被终止，RESULT_VARIABLE将被设置为一个提示”超时“的字符串

- RESULT_VARIABLE

该变量将被设置为包含最后一个子进程的结果。这将是来自最后一个子节点的整数返回代码或描述错误条件的字符串

- RESULTS_VARIABLE <variable>

该变量将按照给定的COMMAND参数的顺序，以分号分隔的列表的形式包含所有进程的结果。每个条目将是来自相应子条目的一个整数返回代码或秒数错误条件的字符串。

- OUTPUT_VARIABLE, ERROR_VARIABLE

变量名将分别用标准输出管道和标准错误管道的内容设置。如果为两个管道命名相同的变量，他们的输出将按生成的顺序合并

- INPUT_FILE, PUTPUT_FILE, ERROR_FILE

命名的文件将分别附加到第一个进程的标准输入、最后一个进程的标准输出或所有进程的标准错误

如果同一个文件同时被输出和错误命名，那么他将同时被使用。

- OUTPUT_QUIET, ERROR_QUIET

标准输出或标准错误结果将被悄悄地忽略。

- COMMAND_ECHO <where>

正在运行的命令将回显到\<where\>，而\<where\>设置为STDERR、STDOUT或NONE之一

- ENCODING <name>

在Windows上，用于解码进程输出的编码。在其他平台上被忽略。有效的编码名称是：

NONE-执行没有解码。这假设过程输出的编码方式与CMake的内部编码（UTF-8）相同。这是默认值。

AUTO-使用当前活动控制台的代码页，如果不可用，则使用ANSI

ANSI-使用ANSI代码页

OEM-使用原始设备制造商（OEM）代码页

UTF-8 or UTF8-使用UTF-8代码页

- ECHO_OUTPUT_VARIABLE, ECHO_ERROR_VARIABLE

标准输出或标准错误不会被专门重定向到已配置的变量。

输出将被重复，他将被发送到配置的变量中，也在标准输出或标准错误上

- COMMAND_ERROR_IS_FATAL <ANY|LAST>

后面的选项决定遇到错误时的行为：

ANY-如果命令列表中的任何一个命令失败，execute_process()命令将停止并报错。

LAST-如果命令列表中的最后一个命令失败，execute_process()命令将停止并报错。列表中前面的命令不会导致致命错误。

##### 1.20 add_compile_options()



##### 1.21 add_definitions()



##### 1.22 message()



#### 2 判断语句

用法：根据某个宏确定编译内容。比较字符串，相同返回true

```CMAKE
if (CPU_PLATFORM_BIT STREQUAL "64")
	add_library(mylib generic_64bit.c)
else()
	add_library(mylib generic_32bit.c)
endif()
```

