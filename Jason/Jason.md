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



**结构体**

```
typedef struct cJSON {
       struct cJSON*next,*prev;           /* 遍历数组或对象链的前向或后向链表指针*/
       struct cJSON *child;                   /*数组或对象的孩子节点*/
       int type;                                     /* key的类型*/
       char *valuestring;                       /*字符串值*/
       int valueint;                                /* 整数值*/
       double valuedouble;                    /* 浮点数值*/
       char *string;                               /* key的名字*/
} cJSON;
```

**说明：**

1. cJSON是使用链表来存储数据的，其访问方式很像一颗树。每一个节点可以有兄弟节点，通过next/prev指针来查找，它类似双向链表；每个节点也可以有孩子节点，通过child指针来访问，进入下一层。只有节点是对象或数组时才可以有孩子节点

2. type是键（key）的类型，一共有7种取值，分别是：False，Ture，NULL，Number，String，Array，Object

	- 若是Number类型，则valueint或valuedouble中存储着值。若期望的是int，则访问valueint，若期望的是double，则访问valuedouble，可以得到值。

	- 若是String类型的，则valuestring中存储着值，可以访问valuestring得到值。

3. string中存放的是这个节点的名字，可理解为key的名称。

### 二、函数

#### 1、`cJSON_Parse`

> 从缓冲区中解析出`JSON`结构

- `extern cJSON *cJSON_Parse(const char *value);`

解析一块`JSON`数据返回`cJSON`结构，在使用完之后调用`cJSON_Delete`函数释放`json`对象结构

#### 2、`cJSON_Print`

> 将传入的`JSON`结构转化为字符串

- `extern char *cJSON_Print(cJSON *item);`

可用于输出到输出设备，使用完之后`free(char *)`

#### 3、`cJSON_Delete`

> 将`JSON`结构所占用的数据空间释放

- `extern void cJSON_Delete(cJSON *c);`

#### 4、`cJSON_Create**()`

> 创建json

- **创建相应类型**
   - `extern cJSON *cJSON_CreateNull(void);  `
   - `extern cJSON *cJSON_CreateTrue(void);  `
   - `extern cJSON *cJSON_CreateFalse(void);  `
   - `extern cJSON *cJSON_CreateBool(int b);  `
   - `extern cJSON *cJSON_CreateNumber(double num);  `
   - `extern cJSON *cJSON_CreateString(const char *string);  `
   - `extern cJSON *cJSON_CreateArray(void);  `
   - `extern cJSON *cJSON_CreateObject(void);`
- **创建数组类型**
   - `extern cJSON *cJSON_CreateIntArray(const int *numbers,int count);  `
   - `extern cJSON *cJSON_CreateFloatArray(const float *numbers,int count);  `
   - `extern cJSON *cJSON_CreateDoubleArray(const double *numbers,int count);  `
   - `extern cJSON *cJSON_CreateStringArray(const char **strings,int count);  `

#### 5、`cJSON_AddItemTo**()`

> 添加Item

- `extern void cJSON_AddItemToArray(cJSON *array, cJSON *item);  `
- `extern void cJSON_AddItemToObject(cJSON *object,const char *string,cJSON *item);  `
- `extern void cJSON_AddItemToObjectCS(cJSON *object,const char *string,cJSON *item);`

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


**注意：**

- 有归属的json对象itemA调用cJSON_AddItemToObject添加到其他对象中，会带入itemA的next对象

```c++
cJSON *itemA;
itemA = cJSON_GetObjectItem(root_a,"itemA");
cJSON_AddItemReferenceToObject(root_b,"itemA",itemA);
// 在这种情况下，采用cJSON_AddItemReferenceToObject来实现，不要用cJSON_AddItemToObject函数
// itemA的内存释放不归属于root_b，而是归属于root_a
```

###  三、cJSON内存泄露问题注意

[cjson内存泄漏问题注意事项](https://blog.csdn.net/u013564851/article/details/107221600?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-2.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-2.no_search_link)

#### 1、`cJSON_Parse()`

> 将字符串转换为json格式，函数中申请了一块内存给root，所以在最后要释放root

```c++
root = cJSON_Parse(next);
cJSON_Delete(root);	// 释放cJSON_Parse()分配出来的内存空间
```

#### 2、`cJSON_Print()`

含有`cJSON_PrintUnformatted()`函数

> 函数将json数据转换为字符串，这个函数申请了一段内存给out，所以在使用完后也要释放

```c++
out = cJSON_Print(root);
cJSON_free(out);	//  由于out不是json指针格式的数据格式，使用cJSON_free(out)释放即可
```

#### 3、`cJSON_Create**()`

> 将一个字符串转换为json对象，函数里面也涉及内存分配，用完以后也要使用`cJSON_Delete()`释放

```C++
cJSON* json = cJSON_CreateObject();
cJSON* newJson = cJSON_Create**();
cJSON_AddItemToObject(json, "abc", newJson);
```

**注：**

- 若`cJSON* newJson = cJSON_Create**();`创建后，通过`cJSON_AddItemToObject( json, "test", newJson );`（或者`cJSON_AddItemToArray`），加入到数组或者object中，**不需要单独释放newJson** ，删除json时被添加的`item`（此处为申请的`newJson`）同时也会被删除

#### 4、申请释放对应关系

| 申请内存       | 释放内存       |
| -------------- | -------------- |
| `cJSON_Prase`  | `cJSON_Delete` |
| `cJSON_Create` | `cJSON_Delete` |
| `cJSON_Print`  | `cJSON_free`   |
