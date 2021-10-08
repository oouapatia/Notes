>  模板定义语法中关键字`class`与`typename`的作用完全一样，表明后面的符号为一个类型

### 1、C++函数模板

- ```
  template <typename type>
  返回值类型 函数名(参数列表){
  	// 函数主体
  }
  ```

  - **例：**

    ```
    template <typename T>
    inline T const& Max (T const& a, T const& b) 
    { 
        return a < b ? b:a; 
    } 
    ```

### 2、C++类模板

- ```
  template <类型参数表>
  class 类模板名 {
  	成员函数与成员变量
  };
  ```

  - **例：**

    ```
    template <class T>
    class A {
    public:
        void Func(T t);
    };
    ```
  
  - 类型参数表写法
  
    ```
    class类型参数1，class类型参数2，...
    ```
  
    - **例：**
  
      ```
      class T1, class T2
      ```
  
  - 类模板中的成员函数放到类模板定义外面写
  
    ```
    template <类型参数表>
    返回值类型 类模板名 <类型参数名列表>::成员函数名(参数表) {
    	...
    }
    ```
  
    - **例：**
  
      ```
      template <class T>
      void A <T>::Func(T t) {
          cout << t;
      }
      ```
  
  - 用类模板定义对象
  
    ```
    类模板名<真实类型参数表> 对象名(构造函数实际参数列表);
    ```
  
    若类模板有**无参构造函数**
  
    ```
    类模板名<真实类型参数表> 对象名;
    ```
  
    - **例：**
    
      ```
      A<int> a;
      ```

