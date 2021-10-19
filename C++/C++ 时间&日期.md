### 获取当前时间函数

头文件：`#include <time.h>`

#### （1）结构体`tm`

- ```
  struct tm 
  { 　
  	int tm_sec;		 			  /* 秒 [0, 60] */ 　　
  	int tm_min; 	   	 		/* 分 [0, 59] */ 　　
  	int tm_hour; 	  			/* 时 [0, 23] */ 　　
  	int tm_mday;     		  /* 日 [1, 31] */ 　
  	int tm_mon;		  			/* 月 [0, 11]，0代表一月  */ 
  	int tm_year; 	    		  /* 年 [1900, ...] */ 　
  	int tm_wday; 	  			/* 星期 [0, 6]，0代表星期天 */ 　
  	int tm_yday; 	   			 /* 一年中的天数 [0, 365]，0代表1月1日 */ 　
  	int tm_isdst; 	    			/* 夏令时标识符 [-1/0/1]，-1不清楚，0不实行夏令，1实行夏令*/ 　
  	long int tm_gmtoff;	 	/*Seconds east of UTC*/ 
  	const char *tm_zone;  /*当前时区的名字(与环境变量TZ有关)*/ 　
  }；
  ```

#### （2）`time()`函数

- **定义**

  `time_t time(time_t *t);`

- **说明**

  返回从1970年1月1日的UTC时间从0时0分0秒算起到现在所经过的秒数。若指针t不是非空指针，会将返回值存到指针t所指的内存

- **例**

  `time_t currentTime = time((time_t*)NULL);`

#### （3）`localtime_r()`函数

localtime_r 是可重入函数，线程安全

- **定义**

  `struct tm *localtime_r(const time_t *timep, struct tm *result);`

- **说明**

  将系统偏移秒数转化为当前时间，并存在result中

#### （4）`asctime()`函数

- **定义**

  `char *asctime(const struct tm *timeptr);`

- **参数**

  `timeptr`是指向`tm`结构的指针

- **返回值**

  返回一个 C 字符串，包含了可读格式的日期和时间信息 **Www Mmm dd hh:mm:ss yyyy**

  - `Www`表示星期几
  - `Mmm`是以字母表示的月份
  - `dd`表示一月中的第几天
  - `hh:mm:ss`表示时间
  - `yyyy`表示年份

#### （5）例

```
time_t currentTime = time((time_t*)NULL);
struct tm timePtr;
localtime_r(&currentTime, &timePtr);
// 年：timePtr.tm_year + 1900；月：ptitimePtrme.tm_mon + 1
```



