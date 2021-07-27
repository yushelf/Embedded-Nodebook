 typedef用来声明一个别名，typedef后面的语法，是一个声明。

虽然在功能上，typedef可以看作一个跟int PARA分离的动作，但语法上typedef属于**存储类声明说明符**，因此严格来说，typedef int PARA整个是一个完整的声明。

定义一个函数指针类型。
  比如原函数是  void  func(void);
  那么定义的函数指针类型就是typedef  void  (*Fun)(void);
  然后用此类型生成一个指向函数的指针：  Fun  func1;
  当func1获取函数地址之后，那么你就可以向调用原函数那样来使用这个函数指针：  func1(void);

