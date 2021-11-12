[toc]

[Linux命令大全](https://man.linuxde.net/)

### 一、快捷键

- `ctrl+r`：查找使用过的命令
- `ctrl+u`：从当前位置清空至开头
- `ctrl+k`：从当前位置清空至结尾
- `ctrl alt F6`：进命令行界面
- `ctrl alt F1`：切回图形界面

### 二、`linux`操作

#### 1、关闭应用自启动

1. 查找开机自启的应用

   `systemctl list-unit-files |grep enabled`

2. 停止服务

   `systemctl stop *.service`

3. 取消开机运行

   `systemctl disable *.service`

| 操作 | 命令 |
| ---- | ---- |
| `systemctl list-unit-files` | 显示出开启的和未开启的程序 |
|`systemctl is-enabled servicename.service`|查询服务是否开机启动|
|`systemctl enable *.service`| 开机运行服务|
|`systemctl disable *.service`|取消开机运行|
|`systemctl start *.service`|启动服务|
|`systemctl stop *.service`|停止服务|
|`systemctl restart *.service`|重启服务|
|`systemctl reload *.service`|重新加载服务配置文件|
|`systemctl status *.service`|查询服务运行状态|

#### 2、查看系统版本号

- `cat /etc/issue`
- `cat /etc/lsb-release`
- `uname -a`

### 三、命令

#### 1、`scp`

- `scp [-r] xxx@_._._._:/home/xxx/  /home/xxx/`：从远处拷贝至自己电脑
  
  **参数：**
  
  - `-r`：递归复制
  - `xxx`：对方用户名
  - `_._._._`：ip地址
  - `/home/xxx/`：第一个为远端存放地址，第二个为自己存放位置

#### 2、`chown`

- `sudo chown xxx -R /dev/kvm`：Android Studio虚拟机打不开

#### 3、`df`

- `df`：用来检查linux服务器的文件系统的磁盘空间占用情况
  
  **参数：**
  
  - `-a`：全部文件系统列表
  - `-h`：方便阅读方式显示

#### 4、`find`

在指定目录下查找文件

- `find path -option [-print] [-exec 或 -ok 或 |xargs 或 |grep] [command {} \;]`

  **参数：**

  - `-option`：

    - `-name/-iname filename`：按文件名查找，`-iname`会忽略大小写
    - `-perm`：按权限查找
    - `-user uasername`：按文件作者查找
    - `-group`：按组查找
    - `-type b/d/c/p/l/f`：查找为块设备、目录、字符设备、管道、符号链接、普通文件的文件
    - `-size [-/+]n[c/M/k]`：文件大小是n单位
      - `-`代表小于，`+`代表大于
    - `-prune`：忽略某个目录

  - `-print`：表示将结果输出带标准输出

  - `-exec`、`-ok`、`|args`：对匹配的文件执行该参数所给出的shell命令

    - 形式为`command {} \;`

      例：`-exec ls -l {} \;`

    - `-ok`在执行前会给出提示让用户确认

- 若不加参数则会在当前目录下查找子目录与文件

#### 5、`grep`

查找文件里符合条件的字符串

- `grep [-options] "搜索内容" 文件名`

  **options：**

  - `-option`：

    `-A/B<n>`：显示符合样式行及之后（前）的n行

    `-C<n>`：显示符合样式行及之前以及后前的n行

    `-a`：不忽略二进制数据

    `-b`：显示符合样式行及第一个字符的编号

    `-c`：统计找到的符合条件的字符串的次数

    `-h`：不显示匹配文件名

    `-i`：忽略大小写的差别

    `-L`：显示不包含样式行的文件名称

    `-l`：显示包含样式行的文件名称

    `-n`：列出所有的匹配的文本行并显示行号

    `-r`：递归查找

    `-v`：只显示不匹配的文本行

#### 6、`awk`

把文件逐行读入，（空格，制表符）为默认分隔符将每行切片，切开的部分再进行各种分析处理

- `awk [options] 'commands' input-file(s)`

  **options：**

  - `-F 分隔符`：缺省为制表符
  - `-v var=value`：赋值一个变量
  - `-f 脚本文件`：从脚本文件中读取awk命令
  - `-W version`：打印bug报告信息的版本

  `awk`会根据分隔符将行分成若干字符，`$0`为整行，`$1`为第一个字段，`$2`为第二个字段...

  **例：**

  - `awk '{print $1,$2}' 1.txt`
  - `awk -F , '{print $1,$2}' 1.txt`
  - `awk -f 1.awk 1.txt`
  - `awk -v a=1 '{print $1,$1+a}' 1.txt`

- **支持内置变量**

  | 变量名     | 解释                                   |
  | ---------- | -------------------------------------- |
  | `$n`         |     当前记录的第n个字段，字段由FS分隔                                   |
  | `$0`   |   完整的输入记录   |
  | `FILENAME` | `awk`浏览的文件名                      |
  | `FS`       | 设置输入字符分隔符，等价命令行`-F`选项 |
  | `FNR` | 各文件分别计数的行号|
  | `IGNORECASE` | 如果为真，则进行忽略大小写匹配 |
  | `NF`       | 浏览记录的字段个数                     |
  | `NR`       | 已读的记录数                           |

  例：`awk -F : '{print NR "," NF "," $0}' /etc/passwd`

- **支持函数**

  | 函数          | 作用                           |
  | ------------- | ------------------------------ |
  | `toupper(s)`  | 返回s的大写                    |
  | `tolower(s)`  | 返回s的小写                    |
  | `length(s)`   | 返回s长度                      |
  | `substr(s,p)` | 返回字符串s中从p开始的后缀部分 |

  例：`awk '{print toupper("qwer")}'`

- **支持条件操作，正则表达式匹配**

  | 操作符                      | 描述                  |
  | --------------------------- | --------------------- |
  | `<` `<=` `==` `!=` `>` `>=` | 关系运算符            |
  | `?:`                        | 条件表达式            |
  | `||` `&&`                   | 逻辑运算符            |
  | `+` `-` `*` `/` `%`         | 算术运算符            |
  | `~` `!~`                    | 匹配/不匹配正则表达式 |
  | `in`                        | 数组成员              |

  例：`awk '$0 ~ /[ace]/' /etc/passwd`

#### 7、`sed`

`sed`是一种流编辑器，利用脚本来处理文件。处理时，把当前处理的行储存在临时缓冲区中，称为“模式空间”，接着用`sed`命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。

未使用重定向输出，`sed`处理的文件内容并未改变

- ```
  sed [options] 'command' file(s)
  sed [options] -f scriptfile file(s)
  ```

  **options：**

  - `-e`：默认，直接在命令行模式进行`sed`动作编辑，即`command`
  - `-f`：将`sed`动作放在脚本文件中，`-f scriptfile`执行`sed`动作
  - `-i`：直接修改文件内容
  - `-n`：只打印模式匹配的行
  - `-r`：支持扩展表达式
  - `-h`：显示帮助
  - `-v`：显示版本信息

  **command：**

  - `[n]a\string`：在a行下插入string后输出
  - `[n]i\string`：在a行前插入string后输出
  - `[n]c\string`：把a行改为string
  - `[n]d`：删除选择的行
  - `[start, end]s/old/new/g`：从start行到end行将old字符串替换为new字符串
    - 不加g只替换每行第一个，加了g则替换每行的所有
  - `'w file1' file2 `：将`file2`的内容写并追加到`file1`末尾

#### `awk`、`sed`、`grep`比较

-  `grep` 更适合单纯的查找或匹配文本
-  `sed` 更适合编辑匹配到的文本
-  `awk` 更适合格式化文本，对文本进行较复杂格式处理

#### 8、|

`|`左右被理解为简单命令，即前一个（左边）简单命令的==标准输出==指向后一个（右边）标准命令的==标准输入==

#### 9 合并分包为一个压缩包

`cat *.tar.gz.* > log.tar.gz`
