[toc]

### 一、介绍

`JSON`语法是JavaScript对象表示法语法的子集。

- 数据在`键/值`对中；
- 数据由分号（`;`）分隔开
- 花括号（`{}`）保存对象，也称一个文档对象
- 方括号（`[]`）保存数组
- 每个数组成员用逗号（`,`）隔开，并且每个数组成员可以是`文档对象`或者`数组`或者`键值对`



`JSON`基于两种结构

- `名称/值对的集合`（`A collection of name/value pairs`）

  不同的编程语言中，被理解为对象（`object`），记录（`record`），结构（`struct`），字典（`dictionary`），哈希表（`hashtable`），有键列表（`keyed list`），或者有关数组（`associative array`）

- `值的有序列表`（`An ordered list of values`）

  大部分语言中，被实现为数组（`array`），矢量（`vector`），列表（`list`），序列（`sequence`）



`JSON`的三种语法

- 键/值对 `key:value`，用`:`分隔开

  如：`"name":"key"`

- 文档对象`JSON`对象写在花括号（`{}`）中，可以包含多个键/值对

  如：`{"name":"123", "address":"cq"}`

- 数组`JSON`在方括号（`[]`）中写：数组成员可以是对象、值、也可以是数组

  如：`{"love":["乒乓球", "羽毛球", "LOL"]}`

### 二、使用

c语言函数库写`JSON`文件：

#### 1、从缓冲区中解析出`JSON`结构：

`extern cJSON *cJSON_Parse(const char *value);`

解析一块`JSON`数据返回`cJSON`结构，在使用完之后调用`cJSON_Delete`函数释放`json`对象结构

#### 2、将传入的`JSON`结构转化为字符串

`extern char *cJSON_Print(cJSON *item);`

可用于输出到输出设备，使用完之后`free(char *)`

#### 3、将`JSON`结构所占用的数据空间释放

`void cJSON_Delete(cJSON *c);`

#### 4、创建一个值类型的数据

`extern cJSON *cJSON_CreateNumber(double num);`

`extern cJSON *cJSON_CreateString(const char *string);`

`extern cJSON *cJSON_CreateArray(void);`

#### 5、创建一个对象（文档）

`extern cJSON *cJSON_CreateObject();`

#### 6、数组创建及添加

`cJSON *cJSON_CreateIntArray(const int *number, int count);`

`void cJSON_AddItemToArray(cJSON *array, cJSON *item);`

#### 7、`JSON`嵌套

【向对象中增加键值对】：

- `cJSON_AddItemToObject(root, "rows", 值类型数据相关函数());`

【向对象中增加数组】：

- `cJASON_AddItemToObject(root, "rows", cJSON_CreateArray());`

【向数组中增加对象】：

- `cJSON_AddItemToArray(rows, cJSON_CreateObject());`

#### 8、几个能提高操作效率的宏函数

`#define cJSON_AddNumberToObject(object, name ,n)  cJSON_AddItemToObject(object, name, cJSON_CreateNumber(n))`

`#define cJSON_AddStringToObject(object, name ,s)        cJSON_AddItemToObject(object, name, cJSON_CreateString(s))`

#### 9、c语言库函数解析`JSON`文件

- 根据键找`json`节点

  `extern cJSON *cJSON_GetObjectItem(cJSON *object, const char *string)`

- 判断是否有key是string的项，若有返回1，否则返回0

  ```
  extern int cJSON_HashObjectItem(cJSON *object, const char *string) {
  	return cJSON_GetObjectItem(object, string)?1:0;
  }
  ```

- 返回数组结点array中成员的个数

  `extern int cJSON_GetArraySize(cJSON *array);`

- 根据数组下标`index`取`array`数组结点的第`index`个成员，返回该成员结点

  `extern cJSON *cJSON_GetArrayItem(cJSON *array, int index);`

- 遍历数组

  ```
  #define cJSON_ArrayForEach(pos, end)
  for (pos = (head)->child; pos != NULL; pos = pos->next) {}
  ```

  

