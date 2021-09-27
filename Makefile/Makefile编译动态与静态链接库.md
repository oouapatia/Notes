[toc]

- `CFLAGS` 表示用于 C 编译器的选项，
  `CXXFLAGS`表示用于 C++ 编译器的选项。
  这两个变量实际上涵盖了编译和汇编两个步骤。
- `CFLAGS`与`CXXFLAGS`作为环境变量指示`GCC`(即GNU编译器集合)编译源代码过程中使用哪些选项，`CFLAGS`用于`C`代码，`CXXFLAGS`用于`C++`代码
  - `C`文件` CFLAGS` 和` CPPFLAGS `效果相同，对`C++`文件就只识别 `CPPFLAGS`
- `CFLAGS`： ==指定头文件（.h文件）的路径==，如：`CFLAGS=-I/usr/include -I/path/include`。同样地，安装一个包时会在安装路径下建立一个include目录，当安装过程中出现问题时，试着把以前安装的包的include目录加入到该变量中来。



- `LDFLAGS`与`LIBS`

  - `LDFLAGS`
    - `LDFLAGS`是告诉链接器从哪里寻找库文件
    
    - ==gcc 等编译器会用到的一些优化参数，也可以在里面指定库文件的位置==。用法：`LDFLAGS=-L/usr/lib -L/path/to/your/lib`。每安装一个包都几乎一定的会在安装目录里建立一个lib目录。如果明明安装了某个包，而安装另一个包时，它愣是说找不到，可以抒那个包的lib路径加入的LDFALGS中试一下。
    
  - `LIBS`是==告诉链接器要链接哪些库文件==
  
    - 如`LIBS = -lpthread -liconv`
  
  - 有时候LDFLAGS指定-L虽然能让链接器找到库进行链接，但是运行时链接器却找不到这个库，如果要让软件运行时库文件的路径也得到扩展，那么我们需要增加这两个库给"-Wl,R"：`LDFLAGS = -L/var/xxx/lib -L/opt/mysql/lib -Wl,R/var/xxx/lib -Wl,R/opt/mysql/lib`
  
  
  
- 如果在执行`./configure`以前设置环境变量`export LDFLAGS="-L/var/xxx/lib -L/opt/mysql/lib -Wl,R/var/xxx/lib -Wl,R/opt/mysql/lib"` ，注意设置环境变量等号两边不可以有空格，而且要加上引号（shell的用法）。那么执行configure以后，Makefile将会设置这个选项，链接时会有这个参数，编译出来的可执行程序的库文件搜索路径就得到扩展了。





- `-f` :`make -f <文件名>`参数，指定make命令从哪里读取makefile文件
- `-`：让make忽略该命令的错误。如`-rm`
- `@`：关闭回显。不显示命令，只显示结果。
  - 参数`-s`或`--slient`则是禁止所有执行命令的显示.
- `$(shell pwd)` ：获取当前目录的绝对路径

- `?=` ：变量在之前没有赋值的情况下才会对这个变量进行赋值.

- `:=` ：直接展开定义变量.

- `=` :递归展开，可能陷入无限循环.

- `$@` ：代表规则中的目标文件名.

- `$<` ：代表规则的第一个依赖的文件名.

- `$^` ：代表规则中所有依赖文件的列表，文件名用空格分割.



#### 一、动态链接库

- 一种不可执行的二进制程序文件，它允许程序共享执行特殊任务所必需的代码和其他资源。-
- Windows平台上动态链接库的后缀名是”.dll”，Linux平台上的后缀名是“.so”。Linux上动态库一般是libxxx.so;
- 相对于静态函数库，动态函数库在编译的时候并没有被编译进目标代码中，你的程序执行到相关函数时才调用该函数库里的相应函数，因此动态函数库所产生的可执行文件比较小。由于函数库没有被整合进你的程序，而是程序运行时动态的申请并调用，所以程序的运行环境中必须提供相应的库。动态函数库的改变并不影响你的程序，所以动态函数库的升级比较方便。

#### 二、静态链接库

- 静态库库的名字一般是`libxxx.a`；利用静态函数库编译成的文件比较大，因为整个函数库的所有数据都会被整合进目标代码中，使得编译后的执行程序不需要外部的函数库支持。当然这也会成为他的缺点，因为如果静态函数库改变了，那么你的程序必须重新编译。

#### 三、Makefile

1. 定义变量

   首先定义`SOURCE`，`OBJS`和`TARGET`变量，用于指代我们项目中的源文件、目标文件和可执行文件。

2. 设置编译参数
   `CC`：配置编译器为`g++`，
   `LIBS`：需要调用的链接库（-l开头，去掉lib和.so。

   - 例：对`libopencv_core.so`链接库的调用要写作：`-lopencv_core`）

   `LDFLAGS`：链接库的路径（-L开头）
   `INCLUDE`：头文件的路径。

3. 链接生成
   此步骤生成可执行文件（`ELF`），链接需要用到目标文件，由下一步产生

4. 编译
   此步骤生成目标文件（`.o`）

5. 清理
   此步骤清理可执行文件和所有的目标文件

#### 四、生成可执行程序

```
# source object target
SOURCE := main.cpp func.cpp
OBJS   := main.o func.o
TARGET := main

# compile and lib parameter
CC      := g++
LIBS    :=
LDFLAGS := 
DEFINES :=
INCLUDE :=  -I.
CFLAGS  := 
CXXFLAGS:= 

# link
$(TARGET) : $(OBJS)
	$(CC) -o $@ $^

# compile
$(OBJS) : $(SOURCE)
	$(CC) -c main.cpp -o main.o
	$(CC) -c func.cpp -o func.o

# clean
clean:
	rm -fr *.o
	rm -fr $(TARGET)
```

#### 五、Makefile编译动态链接库

```
# compile and lib parameter
CC      := g++
LIBS    :=
LDFLAGS :=
DEFINES :=
INCLUDE := -I.
CFLAGS  := 
CXXFLAGS:= 

# link parameter
LIB := libfunc.so

#link
$(LIB):func.o
	$(CC) -shared -o -fPIC -o $@ $^
	
#compile
func.o:func.cpp
	$(CC) -c -fPIC $^ -o $@ 

# clean
clean:
	rm -fr *.o
```

1. 将文件编译成`.o`文件
2. 将`.o`文件链接成动态链接库文件
   - 动态链接库的名字一定要以`lib`开头，后缀必须是`.so`
   - 在编译中一定要加上`-shared`，表示时生成动态链接库
3. 会生成一个`.so`库文件

#### 六、调用动态链接库

没有提供`func.cpp`和`func.o`，只是提供了`libfunc.so`这个链接库

- 通过**INCLUDE**指明头文件的路径
- 通过**LDFLAGS** 和 **LIBS**指明动态库的路径和名称。
  - 动态库名称：“掐头去尾”。例：`libfunc.so`库，`LIBS`必须定义为 `-lfunc`，即去掉`lib`和`.so`，在前面加上`lib`
- `libfunc.so`需要放在系统环境变量包含的路径下（比如`/lib`或`/usr/lib`）

```
# target
TARGET := main

# compile and lib parameter
CC      := g++
LIBS    := -lfunc
LDFLAGS := -L/lib/libfunc.so
DEFINES :=
INCLUDE := -I.
CFLAGS  := 
CXXFLAGS:= 

# link
$(TARGET):main.o
	$(CC) -o $@ $^ $(LDFLAGS) $(LIBS)

#compile
main.o:main.cpp
	$(CC) $(INCLUDE) -c $^

clean:
	rm -fr *.o
	rm -fr $(TARGET)
```

#### 七、生成静态链接库

1. 将文件编译成`.o`文件
2. 将这个`.o`文件链接成静态链接库文件
   - 静态链接库同样是`lib`开头，后缀必须是`.a`

**AR：配置链接器为ar**

```
# compile and lib parameter
CC      := g++
LIBS    :=
LDFLAGS := 
DEFINES :=
INCLUDE := -I.
CFLAGS  := 
CXXFLAGS:= 

# link parameter
AR  := ar
LIB := func.a

#link
$(LIB):func.o
	$(AR) -r $@ $^

#compile
func.o:func.cpp
	$(CC) -c $^ -o $@ 

# clean
clean:
	-rm -fr *.o
```

#### 八、调用静态链接库

没有`func.o`也没有`func.so`以及`func.cpp`

- 编译的时候需要通过**INCLUDE**指明头文件的路径
- 链接的时候需要通过**LDFLAGS** 和 **LIBS**指明静态库的路径和名称，这里与动态不同，直接写作`func.a`就行

```
# target
TARGET := main

# compile and lib parameter
CC      := g++
LDFLAGS := -L.
LIBS    := func.a
DEFINES :=
INCLUDE := -I.
CFLAGS  := 
CXXFLAGS:= 

# link
$(TARGET):main.o
	$(CC) -o $@ $^ $(LIBS)
	
#compile
main.o:main.cpp
	$(CC) -c $^ -o $@

# clean
clean:
	rm -fr *.o
	rm -fr $(TARGET)
```

