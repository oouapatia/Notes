> C++11抛弃了动态异常机制（dynamic exception specifications）


> 异常是程序执行期间产生的问题
>
> 异常提供了一种转移程序控制权的方式

### 1、异常处理模型
#### （1）终止模型

- 错误非常关键，以至于程序无法返回到异常发生的地方继续执行

#### （2）恢复模型

- 修正错误，重新尝试调用出问题的方法，通常希望异常被处理后能继续执行程序
  - 如把try块放在while循环中，不断尝试进入try中

### 2、C++处理异常的关键字

- **try**：`try`中包含将要执行但可能会出现异常的代码
- **catch**：通过异常处理程序捕获异常
- **throw**：当问题出现时，抛出异常

### 3、捕获异常

```
try {
	// 保护代码
} catch (ExceptionName e1) {
	// 处理 ExceptionName 异常的代码
} catch (ExceptionName e2) {
	// 处理 ExceptionName 异常的代码
}
```

### 4、抛出异常

> 使用throw在代码块任何地方抛出异常
>
> throw语句的操作数可以使任意的表达式，表达式的结果的类型决定了抛出的异常的类型

### 5、C++ 标准的异常

| 异常                 |                          |                         | 描述                                       |
| -------------------- | ------------------------ | ----------------------- | ------------------------------------------ |
| **`std::exception`** |                          |                         | 该异常所有标准 C++ 异常的父类              |
|                      | `std::bad_alloc`         |                         | 该异常可以通过 `new` 抛出                  |
|                      | `std::bad_cast`          |                         | 该异常可以通过 `dynamic_cast` 抛出         |
|                      | `std::bad_exception`     |                         | 在处理 C++ 程序中无法预期的异常时非常有用  |
|                      | `std::bad_typeid`        |                         | 该异常可以通过` typeid `抛出               |
|                      | **`std::logic_error`**   |                         | 理论上可以通过读取代码来检测到的异常       |
|                      |                          | `std::domain_error`     | 当使用了一个无效的数学域时，会抛出该异常   |
|                      |                          | `std::invalid_argument` | 当使用了无效的参数时，会抛出该异常         |
|                      |                          | `std::length_error`     | 当创建太长的`std::string`时，会抛出该异常  |
|                      |                          | `std::out_of_range`     | 该异常可以通过方法抛出，例如 `std::vector` |
|                      | **`std::runtime_error`** |                         | 理论上不可以通过读取代码来检测到的异常     |
|                      |                          | `std::overflow_error`   | 当发生数学上溢时，会抛出该异常             |
|                      |                          | `std::range_error`      | 当尝试存储超出范围的值时，会抛出该异常     |
|                      |                          | `std::underflow_error`  | 当发生数学下溢时，会抛出该异常             |

### 6、定义新的异常

> 通过继承和重载 **exception** 类来定义新的异常

```
struct MyException : public exception {
	const char* what() const throw() {
		return "My Exception";
	} 
}

try {
	// 代码
} catch (MyException &me) {
	std::cout << me.what() << endl;
} catch (exception &e) {
	// 其他异常
}
```

**注意**

- **异常规格说明**

  - `throw()`不是函数，而是异常规格说明，表示`what`函数可以抛出异常的类型，类型说明放到`()`中

    - `()`中没有类型即声明这个函数不抛出异常

    - 函数后面不加`throw()`代表可以抛出任意类型的异常

    - 异常规格说明是为了让函数使用者知道该函数可能抛出的异常有哪些
