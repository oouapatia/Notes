### 1、signal()函数

> 两个参数；第一个参数为一个整数，代表了信号的编号；第二个参数是一个指向处理函数的指针

- `void (*signal (int sig, void (*func)(int)))(int);`
- `signal(registered signal, signal handler);`

### 2、raise()函数

> 该函数带有一个整数信号编号作为参数

- `int raise(signal sig);`

### 3、信号

| 信号      | 描述                                  |
| --------- | ------------------------------------- |
| `SIGABRT` | 程序的异常终止，如调用abort           |
| `SIGFPE`  | 错误的算术运算，如除以0导致溢出等操作 |
| `SIGILL`  | 检测非法指令                          |
| `SIGINT`  | 程序终止信号                          |
| `SIGSEGV` | 非法访问内存                          |
| `SIGTERM` | 发送到程序的终止请求                  |

#### 4、例

``` 
void signalHandler(int signum) {
	exit(signum);
}

int main() {
	signal(SIGINT, signalHandler); 		// 注册信号 SIGINT 和信号处理程序
	while(++i) {
		if (3 == i) {
			raise(SIGINT);								// 生成信号
		}
	}
}
```



