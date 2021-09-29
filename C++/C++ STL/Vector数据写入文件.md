使用`data()`函数与`fwrite()`函数

- `t.data()`返回一个指针，该指针指向矢量内部用于存储元素的内存数组

- `fwrite()`

  `fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream)`

  - `ptr`：指向要被写入的元素数组的指针
  - `size`：元素大小
  - `nmemb`：元素个数
  - `stream`：指向FILE对象的指针

```
#include <iostream>
#include <vector>

using namespace std;

int main(void) {
    vector<unsigned char> t;
    t.push_back('s');
    t.push_back('b');
    t.push_back('!');
    t.push_back('\n');

    unsigned char* p = t.data();
    cout << p;

    FILE* a = fopen("1.txt", "w");
    fwrite(p, sizeof(unsigned char), 4, a);
    fclose(a);

    return 0;
}
```

