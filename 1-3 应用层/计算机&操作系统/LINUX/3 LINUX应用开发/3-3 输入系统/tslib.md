#### 摘录于韦东山老师课程资料

tslib是一个触摸屏的开源库，可以使用它来访问触摸屏设备，可以给输入设备添加各种“filter”(过滤器，就是各种处理) 

#### tslib框架分析

tslib的主要代码如下：

![img](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20220123145717.jpg)

核心在于“plugins”目录里的“插件”，或称为“module”。这个目录下的每个文件都是一个module，每个module都提供2个函数：read、read_mt，前者用于读取单点触摸屏的数据，后者用于读取多点触摸屏的数据。

 

要分析tslib的框架，先看看示例程序怎么使用，我们参考ts_test.c和ts_test_mt.c，前者用于一般触摸屏(比如电阻屏、单点电容屏)，后者用于多点触摸屏。

一个图就可以弄清楚tslib的框架：

![1](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20220123145702.png)

**形成链表，递归调用！！**

调用ts_open后，可以打开某个设备节点，构造出一个tsdev结构体。

然后调用ts_config读取配置文件的处理，假设/etc/ts.conf内容如下：

 module_raw input

 module pthres pmin=1

 module dejitter delta=100

 module linear

每行表示一个“module”或“moduel_raw”。

对于所有的“module”，都会插入tsdev.list链表头，也就是tsdev.list执行配置文件中最后一个“module”，配置文件中第一个“module”位于链表的尾部。

对于所有的“module_raw”，都会插入tsdev.list_raw链表头，一般只有一个“module_raw”。

注意：tsdev.list中最后一个“module”会指向ts_dev.list_raw的头部。

 

 

无论是调用ts_read还是ts_read_mt，都是通过tsdev.list中的模块来处理数据的。这写模块是递归调用的，比如linear模块的read函数如下：

![img](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20220123145720.jpg)

 

linear模块的read_raw函数如下：

![img](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20220123145722.jpg)

 

因为是递归调用，所有最先使用input模块读取设备节点得到原始数据，再依次经过pthres模块、dejitter模块、linear模块处理后，才返回最终数据。