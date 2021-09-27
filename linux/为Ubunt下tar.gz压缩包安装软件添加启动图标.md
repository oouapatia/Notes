为Ubunt下tar.gz压缩包安装软件添加启动图标

#### 1、修改安装文件夹下的`.sh`文件

软件安装的位置：`/home/puchengwei/pwc/软件安装/dlt_viewer`

**原文件**

```
#!/bin/bash
LD_LIBRARY_PATH_DLT=$(pwd)
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH_DLT
./dlt_viewer
```

- `dlt_viewer`为可执行文件



因为后面会在`/usr/share/applications`里面写`.desktop`文件，即不会在安装路径下执行，如果不修改`./dlt_viewer`会导致找不到执行文件

- `/home/puchengwei/pwc/软件安装/dlt_viewer/startup.sh: 4: /home/puchengwei/pwc/软件安装/dlt_viewer/startup.sh: ./dlt_viewer: not found`

由于原来的`.sh`文件中 使用的相对位置，所以需要修改

- `LD_LIBRARY_PATH_DLT`表示需要使用的库的路径，不修改会导致找不到库文件

- `./`表示当前路径，将其修改为绝对路径

**修改后**

```
#!/bin/bash
LD_LIBRARY_PATH_DLT='/home/puchengwei/pwc/软件安装/dlt_viewer'
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH_DLT
/home/puchengwei/pwc/软件安装/dlt_viewer/dlt_viewer
```

#### 2、添加启动器图标

[为tar软件包创建启动图标](https://blog.csdn.net/weixin_41866130/article/details/104545227)

在`/usr/share/applications`中创建`dlt.desktop`

`dlt.desktop`：

```
Encoding=UTF-8
Name=Dlt
Exec=sh /home/puchengwei/pwc/软件安装/dlt_viewer/startup.sh
Icon=/home/puchengwei/pwc/软件安装/dlt_viewer/ic_pause_sel_36.png
Terminal=false
Type=Application
StartupNotify=true
```