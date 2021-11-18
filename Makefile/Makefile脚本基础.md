Makefile & CMakeLists.txt 脚本

[toc]

### 一、makefile介绍

- makefile关系到整个工程的编译规则
- make命令执行时，需要一个Makefile文件，以告诉make命令需要怎么样的去编译和链接程序

#### 1、Makefile的规则

- Makefile中的第一个目标会被作为其默认目标

target这一个或多个目标表文件依赖于prerequisites中的文件，其生成的规则定义在command中。（prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的文件就会被执行）

```
target ... : prerequisites ...
			command
			...
			...
```

- target：目标文件，可以是object file，也可以是执行文件，还可以是一个标签
- prerequisites：要生成那个target所需要的文件或是目标
- command：make需要执行的命令

例：8个太长，删了5个

```
edit : main.o kbd.o command.o
    cc -o edit main.o kbd.o command.o

main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c

clean :
    rm edit main.o kbd.o command.o
```

- 目标文件（target）包含：执行文件`edit`和中间目标文件`*.o`；

- 依赖文件：即`:`后面那些`.c`和`.h`文件。每一个`.o`都有一组依赖文件，而这些`.o`文件又是执行文件`edit`的依赖文件
- 依赖关系下那行定义了如何生成目标文件的操作系统命令，一定要以一个`Tab`键开头

#### 2、make如何工作

默认方式下：

1. make会在当前目录下找名字叫`Makefile`或`makefile`的文件

2. 若找到，它会寻找文件中的第一个目标文件（target），并把这个文件作为最终的目标文件

3. 若`target`文件不存在，或者`target`所依赖的后面的`.o`文化部的文件修改时间比`target`这个文件新，则执行后面定义的命令来生成`edit`这个文件（可执行文件）

4. 如果`edit`依赖的`.o`文件也不存在，那么make会在当前文件中寻找目标为`.o`文件的依赖性，若找到则再根据那条规则生成`.o`文件
5. `.c`和`.h`文件存在，于是`make`会生成`.o`文件，然后再用`.o`文件生成执行文件`edit`

- 报错：make只管文件的依赖性

  - 在寻找过程中，若出现错误，如最后被依赖的文件找不到，那么make就会直接退出并报错

  - 而对于定义的命令的错误，或是编译不成功，make不会处理

- 如`clean`，没有被第一个目标文件直接或间接关联，它后面所定义的命令不会自动执行；

  - 但可以通过make执行，即`make clean`

#### 3、makefile使用变量

以`$(objects)`这种方式使用变量

```
objects =  main.o kbd.o command.o

edit : $(objects)
	cc -o edit $(objects)
...
```

#### 4、make自动推导

GUN的make可以自动推导文件以及文件依赖关系后面的命令

- make看到一个`.o`文件，它就会自动把`.c`文件加在依赖关系中，且`cc -c whatever.c`也会被推导出来

```
objects = main.o kdb.o command.o

edit : $(objects)
	cc -o edit $(objects)
	
main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h

.PHONY : clean
clean :
    rm edit $(objects)
```

- `.POHONY`表示`clean`是个伪目标文件

#### 5、清空目标文件的规则

- 每个Makefile中都应该写一个清空目标文件（`.o`和执行文件）的规则，利于重编译和保持文件清洁

一般风格：

```
clean:
    rm edit $(objects)
```

更稳健的风格：

```
.PHONY : clean
clean :
    -rm edit $(objects)
```

- 在`rm`命令前加了一个`-`的意思是，某些文件可能会出问题，但不要管，继续做后面的事情
- `clean`规则不要放在文件开头；若放在开头则会变成make的默认目标

#### 6、makefiel的组成

主要包含5个东西：显示规则、隐晦规则、变量定义、文件指示和注释

- 显示规则：说明了如何生成一个或多个目标文件。在书写时指出要生成的文件、文件的依赖和生成的命令
- 隐晦规则：由于make有自动推导功能，可以让我们更加简略的书写
- 变量的定义：变量一般都是字符串，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上
- 文件指示：包括三个部分
  - 在一个Makefile中引用另一个Makefile，类似C语言中的include
  - 根据某些情况指定Makefile中的有效部分，类似C语言中的预编译
  - 定义一个多行命令
- 注释：Makefile中只有行注释，注释用`#`字符；若需要在Makefile中使用`#`字符，可以用`\`进行转义（`\#`）

Makefile中的命令，必须要以`Tab`键开始

#### 7、Makefile的命名

默认情况下，make命令会在当前目录下按顺序寻找文件名为`GNUmakefile`、`makefile`、`Makefile`文件

- 一般使用`Makefile`这个文件名

- 指定别的文件名书写Makefie，如`Make.Linux`、`Make.Solaris`等

  如果要指定特定的Makefile，可以使用`make -d Make.Linux`或者`make --file Make.AIX`

#### 8、引用其它的Makefile

在Makefile使用`include`关键字可以把别的Makefile包含进来，类似C语言的`#include`

语法：`include <filename>`

- `include`前不能以`Tab`开始
- 有`foo.make`，`a.mk`，`b.mk`，`c.mk`，`$(bar)`包括`e.mk`和`f.mk`
  - `include foo.make *.mk $(bar)` = `include foo.make a.mk b.mk c.mk e.mk f.mk`

make命令开始时，会寻找`include`所指出的其它Makefile，并将其内容放在当前位置

若文件均未指定绝对路劲或相对路劲，则make会在当前目录下首先寻找，若当前目录下未找到，make还会在以下目录下寻找：

- 若make执行时，有`-I`或`--include-dir`参数，则make会在此参数指定的目录下寻找
- 若目录`<prefic>/include`（一般为`/usr/local/bin`或`/usr/include`）存在，make也会寻找

- 若文件未找到，会生成一条警告但不会马上出现致命错误。它会继续载入其它的文件，在完成makefile的读取后，make会再次访问这些文件，如果还是不行，make才会出现一条致命信息。

`-include <filename>`：无论include过程中出现什么错误，都不要报错继续执行

### 二、书写规则

#### 1、规则的语法

```
targets : prerequisites
	command
	...
```

或者

```
targets : prerequisites ; command
    command
    ...
```

- `targets`：文件名，以空格分开，可以使用通配符；一般目标为一个文件，但也可能为多个文件

- `command`：命令行，如果不与`prerequisites`放在同一行，则必须以`Tab`开头，若放在同一行，则可以使用`;`分隔

  如果命令太长，可以使用反斜杠（`\`）作为换行

- 一般来说，make会以UNIX的标准Shell，即`/bin/sh`来执行

#### 2、规则中的通配符

- make支持三个通配符：`*`，`?`，`~`

  - `~`：`~/test`表示当前用户的`home`目录下的`test`目录；`~hchen/test`表示用户hchen下的test目录

    而在Windows或MS-DOS下，用户没有宿主目录，`~`所指的目录由环境变量HOME而定

#### 3、文件搜寻

特殊变量`VPATH`（全大写），指定文件的目录

`VAPTH = src:../headers`：指定两个目录，`src`和`../headers`；目录由`:`分隔（当前目录永远时最高有优先搜索的地方）

关键字`vpath`（全小写），可以指定不同的文件在不同的搜索目录中

- 使用方法有三种
  - `vapth <pattern> <directories>`：为符合模式`<pattern>`的文件指定搜索目录`<directories>`
    - `vpath %.h ../headers`
  - `vpath <pattern>`：清除符合模式`<pattern>`的文件的搜索目录
  - `vpath`：清除所有已被设置好的文件搜索目录
- `vpath`使用方法中的`<patttern>`需要包含`%`字符（匹配零或若干字符）

#### 4、伪目标

“伪目标”不是一个文件，只是一个标签

- 伪目标不能和文件重名
  - 为了避免和文件重名，可以使用特殊标记`.PHONY`来显式指明一个目标是“伪目标”
  - 只要有这个声明，不管是否有“clean”文件，只要运行“clean”这个目标，只有“make clean”这样
- 目标可以成为依赖，伪目标也可以成为依赖

#### 5、多目标

自动化变量：`$@` （target）

- 表示目前规则中所有的目标的集合

`$^`表示target后的`prerequisites`

### 三、书写命令

#### 1、显示命令

- make一般会将其将要执行的命令行在命令执行前输出到屏幕上；当使用`@`字符在命令行前，则这条命令不会被make显示出来
- make执行时，带入参数`-n`或`--just-print`，则只会显示命令，不会执行
  - 带参数`-s`或`--silent`或`--quiet`则时全面禁止命令的显示

#### 2、命令执行

- 如果想让上一条命令的结果应用在下一跳命令时，应该使用分号`;`分隔两条命令

#### 3、命令出错

- 当命令运行完后，make会检测每个命令的返回码；若返回成功，则执行下一条；若一个规则中的某个命令出错了（命令退出码非0），则make会终止执行当前规则
- 可以在命令行前加`-`，忽略命令的出错，不管命令出不出错都认为时成功的

#### 4、嵌套执行make





