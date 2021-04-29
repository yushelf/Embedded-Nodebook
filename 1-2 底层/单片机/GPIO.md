## 八总模式

输入模式

  -输入浮空（GPIO_Mode_IN_FLOATING）

  -输入上拉(GPIO_Mode_IPU)

  -输入下拉(GPIO_Mode_IPD)

  -模拟输入(GPIO_Mode_AIN)

输出模式

  -开漏输出(GPIO_Mode_Out_OD)

  -开漏复用功能(GPIO_Mode_AF_OD)

  -推挽式输出(GPIO_Mode_Out_PP)

  -推挽式复用功能(GPIO_Mode_AF_PP)

## 底层原理

**输入浮空：**浮空就是逻辑器件与引脚即不接高电平，也不接低电平，电压不确定，由于内部结构，一般是高电平。浮空一般用来做ADC输入用，这样可以减少上下拉电阻对结果的影响。

![image-20210428205017641](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210428205026.png)

**输入上拉模式：**上拉就是把点位拉高，比如拉到Vcc。

![image-20210428205232077](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210428205233.png)

**输入下拉：**就是把电压拉低，拉到GND。与上拉原理相似

![image-20210428205314893](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210428205316.png)

**模拟输入：**模拟输入是指传统方式的输入，数字输入是输入PCM数字信号，即0,1的二进制数字信号，通过数模转换，

转换成模拟信号，经前级放大进入功率放大器，功率放大器还是模拟的。

![image-20210428205407594](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210428205408.png)