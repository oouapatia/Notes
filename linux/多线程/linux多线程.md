### 动态初始化与静态初始化

- 动态初始化在堆中创建，需要删除以释放内存
- 静态初始化在静态存储区，初始化之后直接用就可以，不需要删除

### `pthread_t`

### `pthread_create()`

**原型**

- `int pthread_create(pthread_t *tidp,  const pthread_attr_t *attr, (void*)(*start_rtn)(void *), void *arg)`

**功能**

- `pthread_create`是类Unix操作系统的创建线程的函数

**说明**

- 函数若线程创建成功，则返回0，若线程创建失败，则返回出错编号。返回成功时，由`tidp`指向的内存单元被设置为新创建线程的线程ID，`attr`参数用于指定各种不同的线程属性，新创建的线程从`start_rtn`函数的地址开始运行，该函数只有一个万能指针参数`arg`，如果需要向`start_rtn`函数传递的参数不止一个，那么需要把这些参数放到一个结构中，然后把这个结构的地址作为`arg`的参数传入



`pthread_create()`中的`attr`参数是一个结构指针，结构中的元素分别对应着新线程的运行属性，主要包括以下几项：

- 

### `pthread_attr_setdetachstate`







