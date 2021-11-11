[[C++ 单例模式总结与剖析](https://www.cnblogs.com/sunchaothu/p/10389842.html)](https://www.cnblogs.com/sunchaothu/p/10389842.html)

[toc]

### 一、单例模式

> 特点：只提供唯一一个类的实例，具有全局变量的特点，在任何位置都可以通过接口获取到那个唯一实例；

#### 1、C++单例实现

##### 1.1 基础要点

- 全局只有一个实例：`static`特性，同时禁止用户自己声明并定义实例（**`把构造函数设为private`**）
- 线程安全
- 禁止复制和拷贝
- 用户通过接口获取实例：**使用`static`类成员函数**

##### 1.2 C++实现单例的几种方式

###### 1.2.1 懒汉式单例——有缺陷的

> 懒汉式（`Lazy-Initialization`）的方法是直到使用时才实例化对象，即直到调用`getInstance`方法的时候才new一个单例的对象，如果不被调用就不会占用内存

```c++
class LazySingleton
{
public:
    static LazySingleton* getInstace() // 使用懒惰初始化，返回值为当这个函数首次被访问时创建的
    {
        if (NULL == instance) {
            instance = new LazySingleton();
        }
        return instance;
    }

private:
    static LazySingleton *instance;
    LazySingleton();
};
LazySingleton *LazySingleton::instance = NULL;
```

最基础的单例实现

**问题：**

1. **线程安全问题**，当多线程获取单例时有可能引发竞态条件；解决办法：加锁
2. **内存泄露**，类中只负责new出对象，却没有负责delete对象，因此只有构造函数被调用，析构函数却没有被调用，因此会导致内存泄露；解决办法：使用共享指针

###### 1.2.2懒汉式单例——线程、内存安全

```C++
#include <memory> // shared_ptr
#include <mutex>
class LazySingletonLock
{
public:
    typedef shared_ptr<LazySingletonLock> Ptr;
    static Ptr getInstance() {
        cout << "Ptr getInstance" << endl;
        if (NULL == instance_ptr) {
            lock_guard<mutex> lk(m_mutex);
            if (NULL == instance_ptr) {
                instance_ptr = shared_ptr<LazySingletonLock>(new LazySingletonLock);
            }
        }
        return instance_ptr;
    }

private:
    static Ptr instance_ptr;
    static mutex m_mutex;
    LazySingletonLock() {
        cout << "加锁与指针的懒汉模式 constructor called!" << endl;
    }
};
LazySingletonLock::Ptr LazySingletonLock::instance_ptr = NULL;	 		// 外部定义
mutex LazySingletonLock::m_mutex;																	// 外部定义

LazySingletonLock::Ptr instance = LazySingletonLock::getInstance();		// 调用
```

**优点：**

- 基于`shared_str`，用了C++比较倡导的`RAII`思想（资源获取就是初始化），用对象管理资源，当`shared_ptr`析构时，new出来的对象也会被`delete`掉，以此避免内存泄露；
- 加了锁，利用互斥量来达到线程安全。此处使用两个`if`判断语句的技术被称为双检索；好处是，只有判断指针为空的时候才加锁，避免每次调用`getInstance`的方法都加锁；

**缺点：**

- 使用智能指针要求用户；使用锁增加开销，代码量增加

###### 1.2.3 懒汉单例（最推荐）——局部静态变量

```c++
class LazySingletonStatic
{
public:
    static LazySingletonStatic& getInstance() {		// 返回引用而不是指针
        cout << "static getInstance" << endl;
        static LazySingletonStatic instance;
        return instance;
    }
private:
    LazySingletonStatic() {
        cout << "局部静态变量 constructor called!" << endl;
    }
};
```

**优点：**

- 保证了并发线程在获取静态局部变量的时候一定是初始化过的，具有线程安全
- C++静态变量的生命周期是从声明到程序结束，所以也是一种懒汉式

**这是一种最推荐的单例实现方式：**

- 通过局部静态变量的特性保证了线程安全
- 不需要使用共享指针，代码简洁
- 注意在使用的时候需要声明单例的引用`LazySingletonStatic&`才能获取对象

###### 1.2.4 懒汉单例——模板实现

```C++
template<typename T>
class SingleTemplate{
public:
    static T& getInstance() {
        cout << "get Instance\n";
        static T instance;
        return instance;
    }
protected:
    SingleTemplate() {
        cout << "模板 constructor called\n";
    }
};
class DeriveSingle:public SingleTemplate<DeriveSingle> {
    friend class SingleTemplate<DeriveSingle>;
public:
private:
    DeriveSingle() = default;
};

```

**注意：**

- 子类需要**将自己作为模板参数T**传递给`SingleTemplate`模板；
- 同时需要将**基类声明为友元**，这样才能调用子类的私有构造函数

**基类模板的实现要点：**

- **构造函数需要是`protected`**，这样子类才能继承
- 使用了**奇异递归模板模式**`CRTP`（`Curious recurring template pattern`）
- `getInstance`方法和局部静态变量方法相同

###### 1.2.5 饿汉单例模式

```C++
class EagerSingleton
{
public:
    static EagerSingleton& getInstance() {
        return instace;
    }
private:
    static EagerSingleton instace;
    EagerSingleton();
};
```





