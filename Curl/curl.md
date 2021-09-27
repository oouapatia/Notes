[LibCurl HTTP部分详细介绍](https://blog.csdn.net/fengshuiyue/article/details/39530093)

### 一、LibCurl基本编程框架

libcurl是一个跨平台的网络协议库，支持http、https、ftp、gopher、telnet、dict、file和ldap协议。



在基于LibCurl的程序中，主要采用callback function（回调函数）的形式完成传输任务，用户在启动传输前设置好各类参数和回调函数，当满足条件时libcurl将调用用户的回调函数实现特定功能。

利用libcurl完成传输任务的流程：

1. 调用`curl_global_init()`初始化libcurl
2. 调用`curl_easy_init()`函数得到easy interface型指针
3. 调用`curl_easy_setopt()`设置传输选项
4. 根据`curl_easy_setopt()`设置的擦混输选项，实现回调函数以完成用户的特定任务
5. 调用`curl_easy_perform()`函数完成传输任务
6. 调用`curl_easy_cleanup()`释放内存



ibcurl支持3种不同的接口调用方式，分别是`easy`、`multi`和`share`模式

- libcurl-easy是一组**同步**接口；**调用curl_easy_perform()函数进行URL数据传输，直到传输完成函数才返回**；
- libcurl- multi是一组**异步**接口；调用**curl_multi_perform()函数进行传输**，但是**每次调用只传一片数据**，我们可以用select()函数控制多个下载任务进行同步下载，来实现在一个线程中同时下载多个文件；
- libcurl-share允许在多线程中操作**共享数据**

### 二、基本函数

#### 1、`CURLcode curl_global_init(long flags);`

此函数只能用一次。（其实在调用`curl_global_cleanip`函数后仍可再用）

如果这个函数在`curl_easy_init`函数调用时还没调用，它将由`libcurl`库自动调用，==故多线程最好主动调用该函数以防止在线程中curl_easy_init时多次调用。==

- 注意：虽然libcurl是线程安全的，但curl_global_init是不能保证线程安全的，所以不要在每个线程中都调用curl_global_init，应该将该函数的调用放在主线程中
- 参数：flags：
  - CURL_GLOBAL_ALL              //初始化所有的可能的调用
  - CURL_GLOBAL_SSL              //初始化支持安全套接字层
  - CURL_GLOBAL_WIN32        //初始化win32套接字库
  - CURL_GLOBAL_NOTHING      //没有额外的初始化

#### 2、`oid curl_global_cleanup(void);`

在结束libcurl使用的时候，用来对curl_global_init做的工作清理。类似于close的函数

#### 3、`CURL *curl_easy_init( );`

curl_easy_init用来初始化一个CURL的指针。相应的在调用结束时要用curl_easy_cleanup函数清理。
一般curl_easy_init意味着一个会话的开始。它会返回一个easy_handle(CURL*对象), 一般都用在easy系列的函数中。

#### 4、`void curl_easy_cleanup(CURL *handle);`

这个调用用来结束一个会话.与curl_easy_init配合着用

#### 5、`CURLcode curl_easy_setopt(CURL *handle, CURLoption option, parameter);`

几乎所有的curl 程序都要频繁的使用它.它告诉curl库.程序将有如何的行为. 比如要查看一个网页的html代码等.(这个函数有些像ioctl函数)参数:

1. CURL类型的指针
2. 各种CURLoption类型的选项
3. parameter 这个参数 既可以是个函数的指针,也可以是某个对象的指针,也可以是个long型的变量.它用什么这取决于第二个参数

#### 6、`CURLcode curl_easy_perform(CURL *handle);`

这个函数在初始化CURL类型的指针 以及curl_easy_setopt完成后调用。就像字面的意思所说perform就像是个舞台。让我们设置的option 运作起来

### 三、curl_easy_setopt函数部分选项

1、CURLOPT_URL

- 字符串类型，该选项**设置要处理的URL地址**

2、CURLOPT_FOLLOWLOCATION

-  布尔值类型，该参数设置**为非零值表示follow服务器返回的重定向信息**

3、CURLOPT_POSTFIELDS

-  字符串类型，**提交http的post操作字符串数据**

4、CURLOPT_TIMEOUT

-   long数值类型，**设置函数执行的最长时间，时间单位为s**

5、CURLOPT_CONNECTTIMEOUT

-   long数值类型，**设置连接服务器最长时间，时间单位为s；当置为0时表示无限长**

6、CURLOPT_MAX_RECV_SPEED_LARGE

-   curl_off_t类型数据，**指定下载过程中最大速度，单位bytes/s**。

7、**CURLOPT_HEADERFUNCTION**

-  **函数指针类型**，该选项**设置一个处理接收到的header数据的回调函数**，函数原型为：
    size_t function( void *ptr, size_t size, size_t nmemb, void *stream)；
    其中，**ptr指向接收到的header头数据，数据大小为size\*nmemb**，**stream指向调用CURLOPT_WRITEHEADER选项设置的参数**。该回调函数应返回实际处理的数据量大小，或者出错返回-1。

8、**CURLOPT_WRITEFUNCTION**

- 函数指针类型，该选项**设置一个处理接收到的下载数据的回调函数**，函数原型为：
   ` size_t function( void *ptr, size_t size, size_t nmemb, void *stream);`
    其中，ptr指向接收到的数据，数据大小为`size*nmemb`，`stream`指向调用`CURLOPT_WRITEDATA`选项设置的参数
    如果函数指针置为NULL，则会调用默认的函数，将数据写入到由`CURLOPT_WRITEDATA`指定的`FILE*`中

