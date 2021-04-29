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

## 输入底层原理

**输入浮空：**浮空就是逻辑器件与引脚即不接高电平，也不接低电平，电压不确定，由于内部结构，一般是高电平。浮空一般用来做ADC输入用，这样可以减少上下拉电阻对结果的影响。

![image-20210428205017641](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210428205026.png)

**输入上拉模式：**上拉就是把点位拉高，比如拉到Vcc。

![image-20210428205232077](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210428205233.png)

**输入下拉：**就是把电压拉低，拉到GND。与上拉原理相似

![image-20210428205314893](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210428205316.png)

**模拟输入：**模拟输入是指传统方式的输入，数字输入是输入PCM数字信号，即0,1的二进制数字信号，通过数模转换，

转换成模拟信号，经前级放大进入功率放大器，功率放大器还是模拟的。

![image-20210428205407594](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210428205408.png)

## 输出底层原理

开漏输出：输出端相当于三极管的集电极，要得到高电平状态需要上拉电阻才行，适合于做电流型的驱动，其吸收电流的能力相对强（一般20mA以内）

特点：一般用于电平匹配使用，可以利用外部上拉电阻驱动，减少IC内部的驱动。

![image-20210429210656939](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210429210658.png)

开漏复用功能：可以理解为GPIO口被用作第二功能时的配置情况（即并非作为通用IO口使用）。端口必须配置成复用功能输出模式（推挽或开漏）

推挽式输出：可以输出高，低电平，连接数字器件;推挽结构一般是指两个三级管分别受到互补信号的控制，总是在一个三极管导通的时候另一个截止。高低电平由IC的电源低定。

![image-20210429210922325](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210429210923.png)

推挽式复用功能：可以理解为GPIO口被用作第二功能时的配置情况（并非作为通用IO口使用）

**在STM32中选用IO模式**
(1) 浮空输入\_IN_FLOATING --浮空输入，可以做KEY识别，RX1
(2)带上拉输入\_IPU--IO内部上拉电阻输入
(3)带下拉输入_IPD-- IO内部下拉电阻输入
(4) 模拟输入\_AIN --应用ADC模拟输入，或者低功耗下省电
(5)开漏输出\_OUT_OD --IO输出0接GND，IO输出1，悬空，需要外接上拉电阻，才能实现输出高电平。当输出为1时，IO口的状态由上拉电阻拉高电平，但由于是开漏输出模式，这样IO口也就可以由外部电路改变为低电平或不变。可以读IO输入电平变化，实现C51的IO双向功能
(6)推挽输出\_OUT_PP --IO输出0-接GND， IO输出1 -接VCC，读输入 是未知的
(7)复用功能的推挽输出\_AF_PP --片内外设功能(I2C的SCL,SDA)
(8)复用功能的开漏输出\_AF_OD--片内外设功能(TX1,MOSI,MISO.SCK.SS)



**GPIO的主要寄存器**

  每个GPIO端口都有

​    -两个32位配置寄存器（GPIOx_CRL , GPIOx_CRH）

​    -两个32位数据寄存器（GPIOx_IDR 和 GPIOx_ODR）

​    -一个32位置位/复位寄存器（GPIOx_BSRR）

​    -一个16位复位寄存器（GPIOx_BRR）

​    -一个32位锁定寄存器（GPIOx_LCKR）

每个I/O端口位可以自由编程，然而I/O端口寄存器必须按32位字被访问**(不允许半字或字节访问)**

端口配置低寄存器（GPIOx_CRL）

端口配置高寄存器（GPIOx_CRH）

端口输入数据寄存器(GPIOx_IDR)

端口输出数据寄存器(GPIOx_ODR)