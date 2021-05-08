### PB3和PB4和JTAG有映射关系

PB3和PB4在系统复位时候，分别默认为SYS_JIDO和SYS_HJTRST；所以需要通过用户自行禁止其功能；
也就是想要正常使用PB3和PB4的主功能的时候。
在初始化IO时候，增加代码如下：（这里使用J-Link的SWD模式烧录程序）

```c
//打开时钟函数
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB | RCC_APB2Periph_AFIO,ENABLE);	//打开GPIO口时钟，先打开复用才能修改复用功能
GPIO_PinRemapConfig(GPIO_Remap_SWJ_JTAGDisable,ENABLE);//要先开时钟，再重映射；这句表示关闭jtag，使能swd。  
//如果是HAL库，使用 __HAL_AFIO_REMAP_SWJ_NOJTAG();              //禁用JTAG
//接下来按照自己需要配置IO的各种模式就行


标准库“stm32f10x_gpio.h”里面的注释是
#define GPIO_Remap_SWJ_NoJTRST      ((uint32_t)0x00300100)  /*!< Full SWJ Enabled (JTAG-DP + SW-DP) but without JTRST */
#define GPIO_Remap_SWJ_JTAGDisable  ((uint32_t)0x00300200)  /*!< JTAG-DP Disabled and SW-DP Enabled */
#define GPIO_Remap_SWJ_Disable      ((uint32_t)0x00300400)  /*!< Full SWJ Disabled (JTAG-DP + SW-DP) */
```

