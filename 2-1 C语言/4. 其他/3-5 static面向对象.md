static关键字修饰的全局变量，仅在当前文件夹可见，并且该变量的生命周期与整个程序的生命周期一致，若是要修改该变量，需要用该.c文件中的函数去修改，根据这一点特性可以去实现面向对象的一些思想，类似于类的私有变量。

例子：

`main.c`

```c
#include <stdio.h>
#include "Untitled3.h"

int main()
{
	int a = 100;
	int *b = NULL;
	b = fun3(a);
	printf("%d\n",*b);
	return 0;
 } 
```
`Untitled3.c`

```c
#include "Untitled3.h"

static int d = 111;
int *fun3(int a)
{
	int *p = &d;
	printf("d:%d\n",d);
	*p = a;
	return p;
}

```
`Untitled3.h`

```c
#include <stdio.h>
int *fun3(int a);
```

