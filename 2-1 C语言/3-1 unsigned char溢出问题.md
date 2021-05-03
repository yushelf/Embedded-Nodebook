```c
#include<stdio.h>


int main(void)
{
    unsigned char i = 256;
int a;
a = i + 256;
i = a;
printf("%d\n",i);
printf("%d\n",a);

return 0;
}
```

在VC++中的输出结果是：
0
256
Press any key to continue
这说明了 语句 unsigned char 申请的空间 所能存储的数字的范围（也就是unsigned char类型所能表示的数的范围）是 0 — 255(十进制) 一共 256（2的8次方）个
如果说你给 unsigned char 类型的变量赋给大于 255的值 或者 小于0 的值 的话，就会出现溢出，它的规则就像时钟一样，时钟中的最大数字是12，如果
是13点的话，那么时针就会指向1，同样的道理，如果你给 unsigned char 类型的变量 赋 256的话，那么实际上 这个变量存的值为 0 ，在这个例子当中
int 类型的变量所能表示的值 的范围为 -2147483648 — 2147483647 (十进制) 而256 显然在范围之类，所以输出的就是 256 