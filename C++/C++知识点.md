### 1、引用

**引用初始化以后不能被改变，指针可以改变所指的对象**

- 不是指变量的值不可变，而是指向的变量==地址不可变==

**引用**

- 引用就是某一变量（对象）的一个别名，对引用的操作与对变量直接操作完全一样

  ```
  string s1("Nancy");
  string s2("Clancy");
  
  string& rs = s1; 	// rs 引用 s1
  string *ps = &s1; // ps 指向 s1
  
  rs = s2; 					// rs 仍旧引用s1，但是 s1的值现在是Clancy，相当于s1 = s2
  ps = &s2; 				// ps 现在指向 s2，但s1 没有改变
  ```

  声明一个引用，不是重新定义一个变量，只是表示该引用名是目标变量的一个别名，**引用本身不占存储单元，编译器不会给引用分配存储单元。所以对引用取地址，返回结果是目标变量的内存地址**



### 2、虚拟继承

> 在C++中，多重继承不推荐且不常用，离开了多继承，虚拟继承就失去了存在的必要

**环状继承`A->D, B->D, C->(A,B)`**

```
class D{};
class B:public D{};
class A:public D{};
class C:public B, public A{};
```

这种继承会使D创建两个对象，可以使用**虚拟继承格式**

格式：`class 类名:virtual 继承方式 父类名`

```
class D{};
class B:virtual public D{};
class A:virtual public D{};
class C:public B, public A{};
```

