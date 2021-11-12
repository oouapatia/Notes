1、查找指定文件包含`指定路径`、`指定单词`结果输出到指定文件

```sh
#!/bin/bash
grep '/home/pwc/bx/llop/fa' log_build.txt | grep 'error' | grep -v 'tp' > error.txt
grep '/home/pwc/bx/llop/fa' log_build.txt | grep 'warning' | grep -v 'tp' > warning.txt
```

2、在本目录（`./`）下搜索指定字符串

```sh
grep -rn 'cJSON_CreateObject' ./ | grep '.cpp' > cjson.txt
```

