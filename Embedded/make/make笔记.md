## make

### 0 注意事项

1. Makefile文件中的**缩进使用Tab键，不能使用空格**。每个命令行前面必须是一个Tab字符。
2. 

### 1 快速入门

Makefile里主要包含5个内容：**显式规则、隐晦规则、变量定义、文件指示和注释**。

- **显式规则**：显式规则说明了如何生成一个或多个目标文件。由Makefile的书写者明显指出，要生成的文件、文件的依赖文件、生成的命令。
- **隐晦规则**：由于make有自动推导的功能，所以隐晦的规则可以让我们比较粗糙地简略Makefile，这是由make所支持的。
- **变量的定义**：在Makefile中我们要定义一系列的变量，变量一般都是字符串，这个有点像c语言中的宏，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上。
- **文件指示**：包括三个部分，一个是在一个Makefile中引用另一个Makefile，就像c语言中的#include一样；另一个是根据某些情况指定Makefile中的有效部分，就行c语言中的预编译#if一样；还有就是定义一个多行的命令。
- **注释**：Makefile中只有行注释，和UNIX的Shell脚本一样，其注释使用“#”字符，这个就像c/c++中的“//”一样。如果要在Makefile中使用“#”字符，可以用反斜杠进行转义，如：

  ```makefile
  "\#"	# 在makefile中使用#符号，使用反斜杠进行转义
  ```

先来看一个简单的Makefile文件示例：

```makefile
hello: hello.c
	gcc -o hello hello.c		# 命令行必须以Tab缩进
clean:
	rm -f hello					# 命令行必须以Tab缩进
```

#### 1.1 规则

一个Makefile文件包含一系列的“规则”，其样式如下：

```makefile
目标（target）: 依赖（prerequiries）...
<tab>命令（command）
```

**目标（target）**通常是要生成的文件的名称，可以是可执行文件或OBJ文件，也可以是一个执行的动作名称，诸如“clean”。

**依赖（prerequiries）**是用来产生目标的材料（比如源文件），一个目标经常有几个依赖。

**命令（command）**是生成目标时执行的动作，一个规则可以包含有几个命令，每个命令占一行。

**注意：每个命令行前面必须是一个Tab字符，即命令行第一个字符是Tab。**

通常，如果一个依赖发生了变化，就需要规则调用命令以更新或创建目标。但是并非所有的目标都有依赖，例如，目标“clean”的作用是清除文件，它没有依赖。

规则一般是用于解释怎样和何时重建目标。make首先调用命令处理依赖，进而才能创建或更新目标。当然，一个规则也可以是用于解释怎样和何时执行一个动作，即打印提示信息。

一个Makefile文件可以包含规则以外的其他文本，但一个简单的Makefile文件仅仅需要包含规则。

#### 1.2 变量与赋值

##### 1.3.1 变量

Makefile中“$@”、“$^”、“$<”称为自动变量。

- $@：表示规则的目标文件名；
- $^：表示所有依赖的名字，名字之间用空格隔开；
- $<：表示第一个依赖的文件名；
- %：是通配符，他和一个字符串任意个数的字符相匹配；

##### 1.3.2 赋值运算符

在Makefile中经常看到=、:=、?=、+=这几个赋值运算符。他们的区别用前面的例子展示如下：

```makefile
ifdef DEFINE_VRE
	VRE = "Hello World!"	# 基础的赋值操作
else
endif

ifeq ($(OPT), define)			# ifeq后面的空格一定要有
	VRE ?= "Hello World! First!"	# 变量如果没有被赋值，则进行赋值
endif

ifeq ($(OPT), add)			# ifeq后面的空格一定要有
	VRE += "Kelly!"		# 在原值基础上往后追加
endif

ifeq ($(OPT), recover)			# ifeq后面的空格一定要有
	VRE := "Hello World! Again!"	# 覆盖之前的值
endif

all:
	@echo $(VRE)
```

敲入以下make命令（注意=号两边不能有空格）：

```makefile
make DEFINE_VRE=true OPT=define  # 输出：Hello World!
make DEFINE_VRE= OPT=define  # 输出：Hello World! First!
make DEFINE_VRE=true OPT=add  # 输出：Hello World! Kelly!
make DEFINE_VRE= OPT=add  # 输出：Kelly!
make DEFINE_VRE=true OPT=recover  # 输出：Hello World! Again!
make DEFINE_VRE= OPT=recover  # 输出：Hello World! Again!
```

从上面的结果中我们可以清楚的看到它们的区别了

**=** ：是最基本的赋值

**:=** ：是覆盖之前的值

**?=** ：是如果没有被赋值过就赋予等号后面的值，有赋值过就不再操作

**+=** ：是添加等号后面的值

**=与:=的区别！！！** 

使用”=“时，make会将整个makefile展开后，再决定变量的值。也就是说，变量的值将会是整个makefile中最后被指定的值。

```makefile
x = foo
y = $(x) bar
x = opt
```

结果：y的值是opt bar，而不是foo bar。

使用”:=“时，表示变量的值决定于他在makefile中的位置，而不是整个makefile展开后的最终值。

```makefile
x := foo
y := $(x) bar
x := opt
```

结果：y的值将会是foo bar，而不是opt bar。

在GNU make中对变量的赋值有两种方式：延时变量（deferred）、立即变量（immediate）。区别在于它们的定义方式和扩展时的方式不同，前者在在这个变量使用时才扩展开，意即当真正使用时这个变量的值才确定；后者在定义时它的值就已经确定了。使用“=”、“?=”定义或使用define指令定义的变量是延时变量；使用“:=”定义的变量是立即变量。需要注意的一点是“?=”仅仅在变量还没有定义的情况下有效，即“?=”用来定义第一次出现的延时变量。

对于附加操作符“+=”，右边变量如果在前面使用（:=）定义为立即变量则他也是立即变量，否则均为延时变量。

#### 1.3 Makefile常用函数

函数调用的格式如下：

```makefile
$(functin arguments)
```

这里的“function”是函数名，“arguments”是该函数的参数。函数和参数之间用空格或Tab隔开，**如果有多个参数，它们之间用逗号隔开**。这些空格和逗号不是参数值的一部分。

##### 1.3.1 字符串替换和分析函数

**1 $(subst from, to, text)**

在文本“test”中使用“to”替换每一处“from”。

```makefile
$(subst ee, EE, feet on the street)
```

结果为“fEEt on the street”。

**2 $(patsubst pattern, replacement, text)**

寻找“text”中符合格式“pattern”的字，用“replacement”替换他们。“replacement”和“pattern”中可以使用通配符。

```makefile
$(patsubst %.c, %.o, x.c.c bar.c)
```

结果为：“x.c.o bar.o”。

**3 $(strip string)**

去掉前导和结尾空格，并将中间的多个空格压缩为单个空格。

```makefile
$(strip a  b    c   )
```

结果为：“a b c”。

**4 $(findsrting find, in)**

在字符串“in”中搜寻“find”，如果找到，则返回值是”find“，否则返回指为空。

```makefile
$(findstring a, a b c)
$(findstring a, b c)
```

结果为：”a“、” “。

**5 $(filter pattern..., text)**

返回在”text“中由空格隔开且匹配格式“pattern...”的字，去除不符合格式”pattern...“的字。

```makefile
$(filter %.c $.s, foo.c bar.c baz.s ugh.h)
```

结果为：”foo.c bar.c baz.s“。

**6 $(filter-out pattern..., text)**

返回在”text“中由空格隔开且不匹配格式”pattern...“的字，去除符合格式”pattern...“的字。它是函数filter的反函数。

```makefile
$(filter-out %.c %.s, foo.c bar.c baz.s ugh.h)
```

结果为：”ugh.h“。

**7 $(sort list)**

将”list“中的字按字母顺序排序，并去掉重复的字。输出由单个空格隔开的子的列表。

```makefile
$(sort foo bar lose)
```

返回值：”bar foo lose“。

##### 1.3.2 文件名函数

1 $(dir names...)

抽取”names...“中每一个文件名的路径部分，文件名的路径部分包括从文件名的首字符到最后一个斜杠（含斜杠）之前的一切字符。

```makefile
$(dir src/foo.c hacks)
```

结果为：”src/ ./“。

2 $(notdir names...)

抽取”names...”中每一个文件名中除路径部份外一切字符（真正的文件名）。

```makefile
$(notdir src/foo.c hacks)
```

结果为：“foo.c hacks”。

3 $(suffix names)

抽取“names...”中每一个文件名的后缀。

```makefile
$(suffix src/foo.c src-1.0/bar.c hacks)
```

结果为：“.c .c”。

4 $(basename names...)

抽取“names...”中每一个文件名中除后缀外一切字符。

```makefile
$(basename src/foo.c src-1.o/bar hacks)
```

结果为：“src/foo src-1.0/bar hacks”。

5 $(addsuffix suffix, names...)

参数“names..."是一系列的文件名，文件名之间用空格隔开；suffix是一个后缀名。将suffix（后缀）的值附加在每一个独立文件名的后面，完成后将文件名串联起来，它们之间用单个空格隔开。

```makefile
$(addsuffix .c, foo bar)
```

结果为：”foo.c bar.c“。

6 $(addprefix prefix, names...)

参数”names...“是一系列的文件名，文件名之间用空格隔开；prefix是一个前缀名。将prefix（前缀）的值附加在每一个独立文件名的前面，完成后将文件名串联起来，他们之间用单个空格隔开。

```makefile
$(addprefix src/, foo bar)
```

结果为：”src/foo src/bar“。

7 $(wildcard pattern)

参数”pattern“是一个文件名格式，包含有通配符（通配符和shell中的用法一样）。函数wildcard的结果是一列和格式匹配且真实存在的文件的名称，文件名之间用一个空格隔开。

比如，当前目录下有文件1.c、2.c、1.h、2.h，则：

```makefile
c_src := $(wildcard *.c)
```

结果为：”1.c 2.c“。

##### 1.3.3 其他函数

**1 $(foreach var, list, text)**

前两个参数，”var“和”list“将首先扩展，最后一个参数”text"此时不扩展；接着，“list”扩展所得的每个字都赋给“var”变量；然后“text”引用该变量进行扩展，因此“text”每次扩展都不相同。

函数的结果是由空格隔开的“text”在“list”中多次扩展后，得到的新的“list”，就是说：“text”多次扩展的子串连起来，字与字之间由空格隔开，如此就产生了函数foreach的返回指。

下面的例子，将变量“files”的值设置为“dirs"中的所有目录下的所有文件的列表：

```makefile
dirs := a b c d
files := $(foreach dir, $(dirs), $(wildcard $(dir)/*))
```

这里的”text“是”$(wildcard $(dir)/*)“，它的扩展过程如下。

- 第一个赋给变量dir的值是”a“，扩展结果为”$(wildcard a/*)“;
- 第二个赋给变量dir的值是”b“，扩展结果为”$(wildcard b/*)“；
- 第三个赋给变量dir的值是”c“，扩展结果为”$(wildcard c/*)“；
- ......

上面的例子和下面的例子有共同的结果：

```makefile
files := $(wildcard a/* b/* c/* d/*)
```

**2 $(if condition, then-part[, else-part])**

首先把第一个参数”condition“的前导空格、结尾空格去掉，然后扩展。如果扩展为非空字符串，则条件”condition“为真；如果扩展为空字符串，则条件”condition“为假。

如果条件”condition“为真，那么计算第二个参数”then-part“的值，并将该值作为整个函数if的值。

如果条件”condition“为假，并且第三个参数存在，则计算第三个参数”else-part“的值，并将该值作为整个函数if的值；如果第三个参数不存在，函数if将什么也不计算，返回空值。

注意：仅能计算”then-part“和”else-part“二者之一，不能同时计算。这样有可能产生副作用（例如函数shell的调用）。

**3 $(origin variable)**

变量”variable"是一个查询变量的名称，不是对该变量的引用。所以，不能采用“$”和圆括号的格式书写该变量，当然，如果需要使用非常量的文件名，可以在文件名中使用变量引用。

函数origin的结果是一个字符串，该字符串的变量的定义如下：

- undefined：变量“variable"从来没有定义；
- default：变量”variable“是默认定义；
- environment：变量”variable“作为环境变量定义，选项”-e“没有打开；
- environment override：变量”variable“作为环境变量定义，选项”-e：已打开；
- file：变量“variable”在Makefile中定义；
- command line：变量“variable”在命令行中定义；
- override：变量“variable”在Makefile中用override指令定义；
- automatic：变量“variable”是自动变量；

**4 $(shell command arguments)**

函数shell是make与外部环境的通信工具。函数shell的执行结果和在控制台上执行“command arguments”的结果相似。不过如果“command arguments”的结果含有换行符（和回车符），则在函数shell的返回结果中将把他们处理为单个空格，若返回结果最后是换行符（和回车符）则被去掉。

比如当前目录下有文件1.c、2.c、1.h、2.h，则：

```makefile
c_src := $(shell ls *.c)
```

[结果为：“1.c 2.c”。]()

