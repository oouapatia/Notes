[toc]

### 一、系统调用

- 通过系统调用来访问文件是最直接的方式。系统调用函数直接作用于操作系统内核的设备驱动程序从而实现文件访问

#### 1、文件描述符

程序开始运行时，文件一般会有三个已经打开的文件描述符：

- `0`：标准输入
- `1`：标准输出
- `2`：标准错误

其它文件的文件描述符，在调用文件打开函数（`open()`）时返回

#### 2、`write()`

将缓冲区的数据写入文件中

**（1）原型**

`Linux`中`write()`的函数原型

- `size_t write(int fildes, const void *buf, size_t nbytes);`

  **参数**

  - `fildes`：文件描述符，唯一标识了要写入的目标文件
  - `*buf`：待写入的文件，是一个字符串指针
  - `nbytes`：要写入的字符数

**（2）返回值**

- size_t  返回成功写入文件的字符数

**（3）例**

```
int a = open("1.txt", O_WRONLY);
write(a, "here is some data\n", 18);
```

#### 3、`read()`

用文件标识符指定要读取的文件，数据读入一个准备好的缓冲区

**（1）原型**

`Linux`中`read()`的函数原型

- `size_t read(int fildes, void *buf, size_t nbytes);`

  **参数**

  - `fildes`：文件描述符
  - `*buf`：缓冲区，用来存储读入的数据
  - `nbytes`：要读取的字符数

**（2）返回值**

- 返回成功读取的字符数

**（3）例**

- `int n_read = read(a, buf, 20);	// a为fopen的返回值`

#### 4、`open()`

需要添加头文件`<fcntl.h>`

**（1）原型**

`Linux`中`open()`的函数原型

- ```
  int open(const char *path, int oflags);
  int open(const char *path, int oflags, mode_t mode );
  ```

  **参数**

  - `path`：需要打开的文件路径

  - `oflags`：文件的访问模式

    | 模式       | 说明             |
    | ---------- | ---------------- |
    | `O_RDONLY` | 以只读方式打开   |
    | `O_WRONLY` | 以只写的方式打开 |
    | `O_RDWR`   | 以读写的方式打开 |

    `oflags`读写模式可与下面组合作为参数（按位或（`|`）后得到）

    | 模式       | 说明                                           |
    | ---------- | ---------------------------------------------- |
    | `O_APPEND` | 把写入数据追加在文件的末尾                     |
    | `O_TRUNC`  | 把文件长度设为0，丢弃以后的内容                |
    | `O_CREAT`  | 如果需要，就按参数mode中给出的访问模式创建文件 |
    | `O_EXCL`   | 与`O_CREAT`一起调用，确保调用者创建出文件      |

  - `mode`：使用`O_CREAT`标志的open来创建文件时，我们必须使用三个参数格式的open调用。第三个参数mode 是几个标志按位或后得到的

    | mode      | 说明 |
    | --------- | ---- |
    | `S_IRUSR`、`S_IWUSR`、`S_IXUSR` |   文件属主：读，写，执行   |
    | `S_IRGRP`、`S_IWGRP`、`S_IXGRP` |   文件所属组：读，写，执行   |
    | `S_IROTH`、`S_IWOTH`、`S_IXOTH` | 其他用户：读，写，执行 |


**（2）返回值**

- 返回文件的描述符

**（3）例**

- `open("2.txt", O_WRONLY | O_CREAT, S_IRUSR | S_IWUSR | S_IXUSR);`

#### 5、`close()`

close系统调用用于关闭一个文件，close调用终止一个文件描述符`fildes`以其文件之间的关联。文件描述符被释放，并能重新使用

**（1）原型**

- `int close(int fildes);`

**（2）返回值**

- close成功返回1，出错返回-1

#### 6、`lseek()`

移动文件读写流的位置

**（1）函数原型**

- `off_t lseek(int fd, off_t offset, int whence);`

  **参数**

  - `fd`：文件描述符

  - `offset`：相对于`whence`的偏移量

  - `whence`：指定文件指针位置

    | 值         | 说明             |
    | ---------- | ---------------- |
    | `SEEK_SET` | 文件头指针       |
    | `SEEK_CUR` | 文件当前位置指针 |
    | `SEEK_END` | 文件尾指针       |

#### 7、`access()`

`access`函数声明在`unistd.h`头文件中

**（1）描述**

- `access`函数用来判断指定的文件或目录是否存在(`F_OK`)，已存在的文件或目录是否有可读(`R_OK`)、可写(`W_OK`)、可执行(`X_OK`)权限。`F_OK`、`R_OK`、`W_OK`、`X_OK`这四种方式通过access函数中的第二个参数mode指定

**（2）声明**

- `int access(const char *pathname, int mode);`

**（3）返回值**

- 如果指定的方式有效，则此函数返回0，否则返回-1

### 二、I/O库文件操作

#### 1、文件流

在标准`I/O`库中，与文件描述符相对于的叫==流（stream）==，它被实现为指向结构FILE的指针

在启动程序时，有三个文件流是自动打开的：

- `stdin`：标准输入
- `stdout`：标准输出
- `stderr`：标准错误输出

#### 2、`fopen()`

**（1）函数原型**

- `FILE *fopen(cosnt char *filename, const char *mode);`

  **参数**

  - `*filename`：打开文件的文件名

  - `*mode`：打开的方式

    | 打开方式 | 描述                                                    |
    | -------- | ------------------------------------------------------- |
    | `r`      | 以只读的方式打开文件，该文件必须存在                    |
    | `r+`     | 以可读写方式打开文件，该文件必须存在                    |
    | `rb+`    | 读写打开一个二进制文件                                  |
    | `rw+`    | 读写打开一个文本文件                                    |
    | `w`      | 打开只写文件，若文件存在则文件长度清0；若不存在则创建   |
    | `w+`     | 打开可读写文件，若文件存在则文件长度清0，若不存在则创建 |

**（2）返回值**

- 打开文件成功返回一个非空的`FILE *`指针，失败返回`NULL`

#### 3、`fwrite()`

从文件流中写数据

**（1）函数原型**

- `size_t  fwrite(const coid *ptr, size_t size , size_t nmemb, FILE *stream);`

  从指定的数据缓冲区`ptr`中把数据写入文件流

  **参数**

  - `ptr`：要被写入的元素数组的指针
  - `size`：被写入的每个元素的大小
  - `nmemb`：元素的个数
  - `stream`：指向文件的指针

**（2）返回值**

- 返回成功写入的记录个数

**（3）例**

- `fwrite(str, sizeof(str) , 1, fp );`

#### 4、`fread()`

从文件流中读取数据

**（1）函数原型**

- `size_t  fread(void *ptr, size_t size, size_t nmemb, FILE *stream);`

  **参数**

  - `ptr`：存放读取数据的指针
  - `size`：要读取的每个元素的大小
  - `nmemb`：元素的个数
  - `stream`：指向文件的指针

**（2）返回值**

- 成功读取到数据缓冲区的记录个数

**（3）例**

- `fread(buffer, strlen(c)+1, 1, fp);`

#### 5、`fclose()`

关闭指定的文件流stream，这个操作会使所有未写出的数据都写出

**（1）函数原型**

- `int fclose(FILE *stream);`

  **参数**

  - `stream`：指向文件的指针

**（2）返回值**

- 流成功关闭，则该方法返回零。如果失败，则返回 `EOF`

#### 6、`fseek()`

**（1）函数原型**

- `int fseek(FILE *stream, long int offset, int whence);`

  **参数**

  - `stream`：指向文件的指针

  - `offset`：相对于`whence`的指针

  - `whence`：指定文件指针位置

    `whence`值与函数`lseek()`中`whence`值相同

**（2）返回值**

- 成功，则该函数返回0，否则返回非零值

**（3）例**

- `fseek( fp, 7, SEEK_SET );`

