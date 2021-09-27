[toc]

### 1、VS Code

- `launch :program ‘xx/build/Debug/outDebug’ doesn't exist`

  **解决：**

  > 需要配置 Launch.json 里 "program":项为 "${fileDirname}/${fileBasenameNoExtension}"



- `对‘pthread_create’未定义的引用` 或`对‘pthread_join’未定义的引用`等

  **解决：**

  > 在编译时加上参数`-lpthread`
  >
  > g++ xxx.cpp -lpthread -o xxx

  **原因：**

  > 由于pthread 库不是 Linux 系统默认的库，连接时需要使用静态库 libpthread.a，所以在使用线程函数时，在编译中要加 -lpthread参数

### 2、Andriod Studio

- 启动模拟器失败

  解决：

  > 在命令行输入`sudo chown xxx /dev/kvm`

### 3、Ubunt

- ​	文件夹中左边侧栏消失

  **解决：**

  > F9

