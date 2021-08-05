## API基础函数

### 创建线程

```c
int pthread_create( 	  pthread_t *		thread,
					const pthread_attr_t *	attr,
						  void *			(*start_routine)(void*)
						  vodi *			arg		);
```

+ 参数1：利用此结构体与线程交互
+ 参数2：指定该线程可能具有的属性，如栈的大小，优先级调度等。一般设置为NULL.
+ 参数3：表示这个线程该在那个函数中运行，这个为一个回调函数。
+ 参数4：要传递给线程开始执行的函数的参数。

### 等待线程



### 锁



### 条件变量



### 编译运行



## 线程编程注意事项

