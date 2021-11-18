#### 1、常量指针与指针常量


```c++
char greeting[] = "Hello";    
char* p1 = greeting;                      // 指针变量，指向字符数组变量    
const char* p2 = greeting;          // 常量指针即常指针，指针指向的地址可以改变，但是所存的内容不能变    
char const* p2 = greeting;          // 与const char* p2 等价    
char* const p3 = greeting;          // 指针常量，指针是一个常量，即指针指向的地址不能改变，但是指针所存的内容可以改变    
const char* const p4 = greeting;    // 指向常量的常指针，指针和指针所存的内容都不能改变，本质是一个常量
```

#### 2、常对象、常指针、常引用

```c++
A b;                        			// 普通对象，可以调用全部成员函数、更新常成员变量    
const A a;                  	  // 常对象，只能调用常成员函数   
const A *p = &a;            // 常指针    
const A &q = a;              // 常引用
```

