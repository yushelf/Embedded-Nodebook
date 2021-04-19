## extern “C”

​	C++为了和C兼容，在目标文件中的符号管理上，C++有一个用来声明货定义一个C的符号的“extern “C”“关键字用法：

```C
extern "C"{
	int func(int);
	int var;
}
```

C++编译器会将括号中的代码当作C语言处理，所以在括号中C++的名称修饰机制不会起作用。

**使用宏判断，避免C++和C语言的头文件重复：**

```C
#ifdef _cplusplus
extern "C"{
#endid
    
void *memset (void *,int,size_t);
   
#ifdef _cplusplus
}
#endif
```

C++编译器默认定义了宏”_cplusplus“,用上面的代码来判断当前编译单元是不是C++代码，如果是C++编译代码，那么memset就会调用C语言的库文件，如果不是则直接声明。