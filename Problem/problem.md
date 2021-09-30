[toc]

### 1、VS Code编译C++

- `launch :program ‘xx/build/Debug/outDebug’ doesn't exist`

  **解决：**

  > 需要配置 Launch.json 里 "program":项为 `${fileDirname}/${fileBasenameNoExtension}`



- `对‘pthread_create’未定义的引用` 或`对‘pthread_join’未定义的引用`等

  **解决：**

  > 在编译时加上参数`-lpthread`
  >
  > g++ xxx.cpp -lpthread -o xxx

  **原因：**

  > 由于pthread 库不是 Linux 系统默认的库，连接时需要使用静态库 libpthread.a，所以在使用线程函数时，在编译中要加 -lpthread参数
  
  
  
- `不存在从 "std::__cxx11::string" 到 "const char *" 的适当转换函数`

  **解决：**

  > 不要将c++11和c++03的库混用

  **原因：**

  > gcc5以及以后的版本，将`std::string`和`std::list`重写，`std::list`变为`std::__cxx11::list<int>`，`std::string`在c++03库是`std::basic_string<char>`，而在c++11中变为了`std::__cxx11::basic_string<char>`

  

### 2、Andriod Studio

- 启动模拟器失败

  解决：

  > 在命令行输入`sudo chown xxx /dev/kvm`

### 3、Ubunt

- ​	文件夹中左边侧栏消失

  **解决：**

  > F9

