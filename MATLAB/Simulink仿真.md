[Simulink仿真入门](https://mp.weixin.qq.com/s?__biz=MzA4NTM0Mzg5Nw==&mid=2247484671&idx=1&sn=5a33f9a2280e50c639b633e912bd5b8c&scene=19&token=46625227&lang=zh_CN#wechat_redirect)

[toc]

### 一、启动Simulink的方式

- 在`MATLAB`命令窗口输入`Simulink`
- 通过`MATLAB`主窗口的`Simulink`快捷按钮打开

### 二、Simulink模块

#### 1、常用模块（`Commonly Used Blocks`）

一般为Simulink模型的基本构建模块，如：输入、输出、示波器、常数输出、加减运算、乘除运算等；

#### 2、连续函数模块（`Continuous`）

连续函数模块，**为仿真提供连续系统**，主要**用于控制系统的拉氏变换中**，主要为积分环节、传递函数、抗饱和积分、延迟环节等；

#### 3、仪表盘模块（`Dashboard`）

仪表盘模块，主要包含自动指示灯（`Automatic indicator Lamps`）、基本形状、无线图标等；

#### 4、非连续函数模块（`Discontinuities`）

非连续函数模块主要为死区、信号的一阶导数（Rate Limiter模块）、阶梯输出模块（Quantizer模块）、约定信号的输出的上下界Saturation以及Relay环节等；

#### 5、离散函数模块（`Discrete`）

**为仿真提供离散元件**，离散函数模块主要将`拉氏变换`后的**传递函数经Z变换离散化**，从而==实现传递函数的离散化建模，离散化系统容易进行程序移植==，因此广泛应用在各种控制器仿真设计中，具体的离散模块库包括延时Delay环节、导数Difference、离散零极点配置Discrete Zero-Pole，离散时间积分环节等；

#### 6、逻辑控制器模块（`Logic and Bit Operations`）

逻辑控制器**主要逻辑位运算**，在常用的系统建模较少用到，主要有位清除（`Bit Clear`）、位设置（`Bit Set`）、组合逻辑运算（`Combinatorial Logic`）等；

#### 7、查表模块（`Lookup Tables`）

查表模块库，包括`1-D Lookup Table ` 、`2-D Lookup Table`、`Direct Lookup Table(n-D)`等，主要是根据模块参数的定义值对输入输出进行插值映射输出，输出的值定义为Table参数，方便用户进行定义和应用；

#### 8、数学模块（`Math Operations`）

数学模块库，**提供数学运算功能元件**，主要为绝对计算`Abs`、加减运算`Add`、放大缩小倍数运算`Gain`、乘除运算`Product`等，用户根据相应的模型表达式构建相应的不同模块的配合使用，达到相应的表达式计算，该数学模块库基本涵盖了所有的基本运算功能；

#### 9、矩阵运算模块（`Matrix Operations`）

矩阵运算模块，主要包含叉积（`cross product`）、单位矩阵（`Identity Matrix`）等矩阵运算；

#### 10、`Messages & Events`

为基于消息的通信建模

#### 11、模块验证（`Model Verification`）

对模型进行自我验证的模块

#### 12、模型扩充模块（`Model-Wide Utilities`）

进行模型范围的运算

#### 13、子系统模块（`Ports & Subsystems`）

与子系统有关的模块

#### 14、信号属性模块（`Signal Attributes`）

信号属性模块，修改信号的属性

#### 15、信号传送模块（`Signal Routing`）

信号传送模块，传送信号

#### 16、数据输出显示模块（`Sinks`）

数据显示库，为仿真提供输出设备元件，包含有输出端`Out1`、示波器`Scope`、数据显示`Display`等模块，方便用户搭建模型后，进行仿真观察模型输出参数值的变化图；

#### 17、信号源模块（`Source`）

信号源模块，为仿真提供各种信号源，如时钟（`clock`）、常量（`const`）等；

#### 18、字符串操作模块（`String`）

进行字符串操作

#### 19、用户自定义模块（`User-Defined Functions`）

用户自定义模块，该模块主要用于供用户自己抒写相应的程序进行系统仿真，从而实现快速的建模仿真









