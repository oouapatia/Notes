#### 1、智能指针

> 智能指针是一个`RAII`（`Resource acquire is initialization`资源获取就是初始化）类模型，用于动态分配内存，其设计思想是将基本类型指针封装为（模板）类对象指针，并在离开作用域时调用析构函数，使delete删除指针锁指向的内存空间

#### 2、智能指针作用

> 处理内存泄露问题和空悬指针问题

#### 3、智能指针的分类

分为`auto_ptr`、`unique_ptr`、`shared_ptr`、`weak_ptr`四种

##### 1、`auto_ptr`

对于`auto_ptr`实现**独占式拥有**的概念，同一时间只能有一个智能指针可以指向该对象；

但在C++11中被摒弃，其主要问题在于：

- 对象所有权的转移，比如在函数传参过程中，对象所有权不会返还，从而存在潜在的内存崩溃问题；
- 不能指向数组，也不能作为`STL`容器的成员

##### 2、`unique_ptr`

对于`unique_ptr`，实现**独占式拥有**的概念，同一时间只能有一个智能指针可以指向该对象，因为无法进行拷贝构造和拷贝赋值，但是**可以进行移动构造和移动赋值**；

##### 3、`shared_ptr`

对于`shared_ptr`，实现**共享式拥有**的概念，即多个智能指针可以指向相同的对象，该对象及相关资源会在其所指对象不再使用之后，**自动释放与对象相关的资源**；

**实现：**

1. 构造函数中计数初始化为1；
2. 拷贝构造函数中计数值加1；
3. 赋值运算符中，左边的对象引用计数减1，右边的对象引用计数加1；
4. 析构函数中引用计数减1；
5. 在赋值运算符和析构函数中，如果减1后为0，则调用delete释放对象

##### 4、`weak_ptr`

> 不具有普通指针的行为，没有重载`operator*`与`->`

- 对于`weak_ptr`，**解决`shared_ptr`相互引用**时，两个指针的引用计数永远不会下降为0，从而导致死锁问题。而`weak_ptr`是对对象的一种弱引用，可以绑定到`shared_ptr`，但不会增加对象的引用计数

- `weak_ptr`必须从一个`shared_ptr`或者另外一个`weak_ptr`转换而来，不能用new对象进行构造

- 将一个`weak_ptr`绑定到一个`shared_ptr`不会改变`shared_ptr`的引用计数

#### 5、使用

````c++
shared_ptr<int> ptra = make_shared<int>(a);	 // 定义
shared_ptr<int> = ptra2(ptra);									// copy
int *pa = ptra.get();														  // 获取原始指针
int count = ptra.use_count();									  // 获取资源的引用计数

weak_ptr<int> wp(ptra);
if (!wp.expired()) {}														  // expired函数：等价于0 == user_count()
````

#### 6、操作

[【C++】智能指针详解](https://blog.csdn.net/flowing_wind/article/details/81301001)

##### 1、**`shared_ptr`和`unique_ptr`都支持的操作**

| 操作               | 含义                                                         |
| ------------------ | ------------------------------------------------------------ |
| `shared_ptr<T> sp` | 空智能指针，可以指向类型为T的对象                            |
| `unique_ptr<T> up` |                                                              |
| `p`                | 将p用做作一个判断条件，若p指向一个对象，则为true             |
| `*p`               | 解引用p，获得它指向的对象                                    |
| `p->mem`           | 等价于`(*p).mem`                                             |
| `p.get()`          | 返回p中保存的指针；若智能指针释放了其对象，返回的指针所指向的对象也消失了 |
| `swap(p, q)`       | 交换p和q中的指针                                             |
| `p.swap(q)`        |                                                              |

##### 2、**`shared_ptr`独有的操作**

| 操作                    | 含义                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `make_shared<T> (args)` | 返回一个`shared_ptr`，指向一个动态分配的类型为T的对象。<br />使用`args`初始化此对象 |
| `shared_ptr<T>p (q)`    | `p`是`shared_ptr` q的拷贝；<br />此操作会递增q中的计数器。q中的指针必须能转换为`T*` |
| `p = q`                 | p和q都是`shared_ptr`，所保存的指针必须能相互转换<br />此操作会递减p的引用计数，递增q的引用计数；<br />若p的引用计数变为0，则将其管理的原内存释放； |
| `p.unique()`            | 若`p.use_count()`为1，返回true；反之返回false                |
| `p.use_count()`         | 返回与p共享对象的智能指针数量                                |

##### 3、**定义和改变`shared_ptr`**

| 操作                     | 含义                                                         |
| ------------------------ | ------------------------------------------------------------ |
| `shared_ptr<T> p(q)`     | `p`管理内置指针`q`所指向的对象；<br />`q`必须指向`new`分配的内存，且能转换为`T*`类型 |
| `shared_ptr<T> p(u)`     | p从`unique_ptr u`那里接管了对象的所有权；将u置空             |
| `shared_ptr<T> p(q, d)`  | p接管了内置指针q所指向的对象的所有权。q必须能转换为`T*l`类型。<br />p将使用可调用对象d来代替`delete` |
| `shared_ptr<T> p(p2, d)` | p是`shared_ptr`的拷贝，唯一区别就是p将用可调用对象d来代替`delete` |
| `p.reset()`              | 若p是唯一指向其对象的`shared_ptr`，`reset`会释放此对象-----> |
| `p.reset(q)`             | ；若传递了可选的参数内置指针q，会令p指向q，否则会将p置为空-----> |
| `p.reset(q, d)`          | ；若还传递了参数d，将会调用d而不是`delete`来释放q            |

`shared_ptr`和`new`结合使用

- ```c++
	shared_ptr<int> p1 = new int(1024);		//错误：必须使用直接初始化形式
	shared_ptr<int> p2(new int(1024));		//正确：使用了直接初始化形式
	```

##### 4、**`unique_ptr`操作**

| 操作                    | 含义                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `unique_ptr<T> u1`      | 空`unique_ptr`，可以指向类型为T的对象，`u1`会使用`delete`来释放它的指针 |
| `unique_ptr<T, D> u2`   | ；`u2`会使用一个类型为D的可调用对象来释放它的指针            |
| `unique_ptr<T, D> u(d)` | 空`unique_ptr`，指向类型为T的对象，用类型为D的对象d代替`delete` |
| `u = nullptr`           | 释放u指向的对象，将u置为空                                   |
| `u.release()`           | u放弃对指针的控制权，返回指针，并将u置空                     |
| `u.reset()`             | 释放u指向的对象                                              |
| `u.reset(q)`            | 如果提供了内置指针q，令u指向这个对象；否则将u置为空          |
| `u.reset(nullptr)`      |                                                              |

> 虽然不能拷贝或复制`unique_str`，但是可以通过release或reset将指针所有权从一个（非`const`）`unique_str`转移给另一个`unique`
>
> > ```c++
> > // 将所有权从p1（指向string Stegosaurus）转移给p2
> > unique_ptr<string> p2(p1.release()); 				// release将p1置为空
> > unique_ptr<string>p3(new string("Trex"));	// 将所有权从p3转移到p2
> > p2.reset(p3.release());												// reset释放了p2原来指向的内存
> > ```
>
> release返回的指针通常被用来初始化另一个智能指针或给另一个智能指针赋值

##### 5、`weak_ptr`操作

| 操作                | 含义                                                         |
| ------------------- | ------------------------------------------------------------ |
| `weak_ptr<T> w`     | 空`weak_ptr`可以指向类型为T的对象                            |
| `weak_ptr<T> w(sp)` | 与`shared_ptr sp`指向相同对象的`weak_ptr`<br />`T`必须能转换为`sp`指向的类型 |
| `w = p`             | p可以是一个`shared_ptr`或一个`weak_ptr`。赋值后w与p共享对象  |
| `w.use_count()`     | 与w共享对象的`shared_ptr`的数量                              |
| `w.expired()`       | 若`w.use_count()`为0，返回true，否则返回false                |
| `w.lock()`          | 如果`expired`为true，返回一个空`shared_ptr`；<br />否则返回一个指向w的对象的`shared_ptr` |

> 由于对象可能不存在，我们不能使用`weak_ptr`直接访问对象，而必须调用`lock`，此函数检查`weak_ptr`锁指向的对象是否存在

##### 6、**智能指针陷阱：**

1. 不使用相同的内置指针值初始化（或reset）多个智能指针。
2. 不delete get()返回的指针
3. 不使用get()初始化或reset另一个智能指针
4. 如果你使用get()返回的指针，记住当最后一个对应的智能指针销毁后，你的指针就变为无效了
5. 如果你使用智能指针管理的资源不是new分配的内存，记住传递给它一个删除器
