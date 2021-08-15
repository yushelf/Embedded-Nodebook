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

```c
int pthread_join(pthread_t thread, void **retval); 
```

+ 参数1：指定要等待的线程
+ 参数2：指向希望得到的返回值

### 锁

通过锁提供互进入临界区，保护不想被打断的操作。

```c
int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex); 
```

使用上面的锁函数来进入临界区还需要初始化之后才能使用。

静态初始化：

```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
```

动态初始化：

```c
int rc = pthread_mutex_init(&lock,NULL);
assert(rc == 0);//初始化失败需要退出程序
```

### 条件变量

当线程之间必须发生某种信号时，如果一个线程在等待另一个线程继续执行某些操作，条件变量就很有用。

线程库一般提供以下两个函数供使用：

```c
int pthread_cond_wait(pthread_cond_t *cond,pthread_mutex_t *mutex);
int pthread_cond_signal(pthreadcond_t *cond);
```

要使用条件变量，必须要有一个与此条件相关的锁。在调用上诉任何一个函数时，都应该持有这个锁。

同样，这个锁和条件变量都需要初始化，分为静态初始化与动态初始化。

**使用方法：**

等待条件变量一方：

```c
pthread_mutex_lock(&lock);
while(read == 0)
	pthread_cond_wait(&cond,&lock);//此处传入两个参数，调用此函数会让此线程睡眠等待被唤醒，同时传入的锁的参数使得锁在随									 //眠时被释放
pthread_mutex_unlock(&lock);	
```

发送条件变量的一方：

```c
pthread_mutex_lock(&lock);
read = 1;
pthread_cond_signal(&cond);
pthread_mutex_unlock(&lock);
```

### 编译运行

线程相关API需要包含头文件pthread.h才能编译，链接时需要ptehread库，增加-lpthread标记。

例如：

gcc -o main main.c -wall -lpthread 



## 线程编程注意事项

+ **保持简洁：**线程之间的锁和信号的代码过于复杂容易产生缺陷。

+ **让先线程的交互减到最少:**每次交互都应该想清楚,并且验证过的正确的方法来实现.

+ **初始化锁和条件变量:**不初始化可能会出现奇怪的效果

+ **检查返回值:**避免初始化失败或者函数执行失败影响代码

+ **注意传给线程的参数和返回值:**栈上面的参数传递会引起麻烦

+ **每个线程都有自己的栈:**数据交互因在堆或者其他全局区

+ **线程之间总是通过条件变量发送信号:**不要用标记变量来同步
+ **多查手册**