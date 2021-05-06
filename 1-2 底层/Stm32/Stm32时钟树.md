## 1 STM32总线

首先，说点不靠谱的，APB和AHB总线，我个人感觉这个类似于个人PC系统里的北桥和南桥总线。
南桥总线上挂接的都是鼠标、键盘这些慢速的设备，北桥上挂接显卡等高速设备。南桥频率低，北桥频率高。另外，南桥最后也要接到北桥上。

这些感觉都类似于APB和AHB。

- **AHB，是Advanced High performance Bus的缩写，译作高级高性能总线，这是一种“系统总线”。**

AHB主要用于高性能模块(如CPU、DMA和DSP等)之间的连接。AHB 系统由主模块、从模块和基础结构(Infrastructure)3部分组成，整个AHB总线上的传输都由主模块发出，由从模块负责回应。

- **APB，是Advanced Peripheral Bus的缩写，这是一种外围总线。**

APB主要用于低带宽的周边外设之间的连接，例如UART、1284等，它的总线架构不像 AHB支持多个主模块，在APB里面唯一的主模块就是APB 桥。再往下，APB2负责AD，I/O，高级TIM，串口1；APB1负责DA，USB，SPI，I2C，CAN，串口2345，普通TIM。

- **这两者都是总线，符合AMBA规范。**

ARM公司推出的AMBA片上总线受到了广大IP开发商和SoC系统集成者的青睐，已成为一种流行的工业标准片上结构。AMBA规范主要包括了AHB(Advanced High performance Bus)系统总线和APB(Advanced Peripheral Bus)外围总线。

## 2 STM32F1时钟系统

![image-20210506121448396](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210506121450.png)

#### 2.1 STM32F1时钟源

==**在STM32F1中，有五个时钟源，为HSI、HSE、LSI、LSE、PLL。**==

①HSI: High Speed Internal 是高速内部时钟，RC振荡器，频率为8MHz。

②HSE: High Speed External 是高速外部时钟，可接石英/陶瓷谐振器，或者接外部时钟源，频率范围为4MHz~16MHz。

③LSI: Low Speed Internal 是低速内部时钟，RC振荡器，频率为40kHz。

④LSE: Low Speed External是低速外部时钟，接频率为32.768kHz的石英晶体。

⑤PLL: Phase Lock Loop 为锁相环倍频输出，其时钟输入源可选择为HSI/2、HSE或者HSE/2。倍频可选择为2~16倍，但是其输出频率最大不得超过72MHz。
![在这里插入图片描述](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210503091912.png)
用户可通过多个预分频器配置AHB总线、高速APB2总线和低速APB1总线的频率。

- AHB和APB2域的最大频率是72MHZ。
- APB1域的最大允许频率是36MHZ。
- SDIO接口的时钟频率固定为HCLK/2。
- 40kHz的LSI供独立看门狗IWDG使用，另外它还可以被选择为实时时钟RTC的时钟源。
- 另外，实时时钟RTC的时钟源还可以选择LSE，或者是HSE的128分频。
- RTC的时钟源通过RTCSEL[1:0]来选择。
- STM32中有一个全速功能的USB模块，其串行接口引擎需要一个频率为48MHz的时钟源。该时钟源只能从PLL输出端获取，可以选择为1.5分频或者1分频，也就是，当需要使用USB模块时，PLL必须使能，并且时钟频率配置为48MHz或72MHz。
- 另外，STM32还可以选择一个PLL输出的2分频、HSI、HSE、或者系统时钟SYSCLK **输出到MCO脚(PA8)上**。

系统时钟SYSCLK，是供STM32中绝大部分部件工作的时钟源，它可选择为==**PLL输出、HSI或者HSE**，（**一般程序中采用PLL倍频到72Mhz**）==在选择时钟源前注意要判断目标时钟源是否已经稳定振荡。Max=72MHz，它分为2路：
（1）1路送给I2S2、I2S3使用的I2S2CLK,I2S3CLK；
（2）另外1路通过AHB分频器分频（1/2/4/8/16/64/128/256/512）分频后送给以下8大模块使用：

- ① 送给SDIO使用的SDIOCLK时钟。
  - ② 送给FSMC使用的FSMCCLK时钟。
  - ③ 送给AHB总线、内核、内存和DMA使用的HCLK时钟。
  - ④ 通过8分频后送给Cortex的系统定时器时钟（SysTick）。
  - ⑤ 直接送给Cortex的空闲运行时钟FCLK。
  - ⑥ 送给APB1分频器。APB1分频器可选择1、2、4、8、16分频，其输出一路供APB1外设使用(PCLK1，最大频率36MHz)，另一路送给定时器(Timer2-7)2、3、4倍频器使用。该倍频器可选择1或者2倍频，时钟输出供定时器2、3、4、5、6、7使用。
  - ⑦ 送给APB2分频器。APB2分频器可选择1、2、4、8、16分频，其输出一路供APB2外设使用(PCLK2，最大频率72MHz)，另一路送给定时器(Timer1、Timer8)1、2倍频器使用。该倍频器可选择1或者2倍频，时钟输出供定时器1和定时器8使用。另外，APB2分频器还有一路输出供ADC分频器使用，分频后得到ADCCLK时钟送给ADC模块使用。ADC分频器可选择为2、4、6、8分频。
  - ⑧ 2分频后送给SDIO AHB接口使用（HCLK/2）。

#### 2.2 时钟输出的使能控制

在以上的时钟输出中有很多是带使能控制的，如AHB总线时钟、内核时钟、各种APB1外设、APB2外设等。
==**当需要使用某模块时，必需先使能对应的时钟**==。需要注意的是定时器的倍频器，当APB的分频为1时，它的倍频值为1，否则它的倍频值就为2。

- 连接在APB1(低速外设)上的设备有：电源接口、备份接口、CAN、USB、I2C1、I2C2、UART2、UART3、SPI2、窗口看门狗、 Timer2、Timer3、Timer4。注意USB模块虽然需要一个单独的48MHz时钟信号，但它应该不是供USB模块工作的时钟，而只是提供给串行接口引擎(SIE)使用的时钟。USB模块工作的时钟应该是由APB1提供的。

- 连接在APB2(高速外设)上的设备有：GPIO_A-E、USART1、ADC1、ADC2、ADC3、TIM1、TIM8、SPI1、AFIO

- ![在这里插入图片描述](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210503091912.png)
  ![image-20210506175038457](https://gitee.com/wang_chunfeng/pic-go/raw/master/img/20210506175048.png)
  
  **使用HSE时钟，程序设置时钟参数流程：**
  1、将RCC寄存器重新设置为默认值 RCC_DeInit;
  2、打开外部高速时钟晶振HSE RCC_HSEConfig(RCC_HSE_ON);
  3、等待外部高速时钟晶振工作 HSEStartUpStatus = RCC_WaitForHSEStartUp();
  4、设置AHB时钟 RCC_HCLKConfig;
  5、设置高速AHB时钟 RCC_PCLK2Config;
  6、设置低速速AHB时钟 RCC_PCLK1Config;
  7、设置PLL RCC_PLLConfig;
  8、打开PLL RCC_PLLCmd(ENABLE);
  9、等待PLL工作 while(RCC_GetFlagStatus(RCC_FLAG_PLLRDY) == RESET)
  10、设置系统时钟 RCC_SYSCLKConfig;
  11、判断是否PLL是系统时钟 while(RCC_GetSYSCLKSource() != 0x08)
  12、打开要使用的外设时钟 RCC_APB2PeriphClockCmd()/RCC_APB1PeriphClockCmd()

==不配置系统时钟的时候，STM32 会把 HSI 当作系统时钟， HSI=8M，由芯片内部的振荡器提供==

下面是STM32软件固件库的程序中对RCC的配置函数(使用外部8MHz晶振)

```c
void RCC_Configuration(void)

{
  RCC_DeInit();
  RCC_HSEConfig(RCC_HSE_ON);   //RCC_HSE_ON——HSE晶振打开(ON)
  HSEStartUpStatus = RCC_WaitForHSEStartUp();
  if(HSEStartUpStatus == SUCCESS)        //SUCCESS：HSE晶振稳定且就绪
  {   
    RCC_HCLKConfig(RCC_SYSCLK_Div1);  //RCC_SYSCLK_Div1——AHB时钟 = 系统时钟
    RCC_PCLK2Config(RCC_HCLK_Div1);   //RCC_HCLK_Div1——APB2时钟 = HCLK
    RCC_PCLK1Config(RCC_HCLK_Div2);   //RCC_HCLK_Div2——APB1时钟 = HCLK / 2
    FLASH_SetLatency(FLASH_Latency_2);    //FLASH_Latency_2  2延时周期
    FLASH_PrefetchBufferCmd(FLASH_PrefetchBuffer_Enable);       // 预取指缓存使能
    RCC_PLLConfig(RCC_PLLSource_HSE_Div1, RCC_PLLMul_9);    
   // PLL的输入时钟 = HSE时钟频率；RCC_PLLMul_9——PLL输入时钟x 9
    RCC_PLLCmd(ENABLE);
    while(RCC_GetFlagStatus(RCC_FLAG_PLLRDY) == RESET) ;    
    RCC_SYSCLKConfig(RCC_SYSCLKSource_PLLCLK);
   //RCC_SYSCLKSource_PLLCLK——选择PLL作为系统时钟
    while(RCC_GetSYSCLKSource() != 0x08);        //0x08：PLL作为系统时钟
  }

  RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA | RCC_APB2Periph_GPIOB |   RCC_APB2Periph_GPIOC , ENABLE);
//RCC_APB2Periph_GPIOA    GPIOA时钟
//RCC_APB2Periph_GPIOB    GPIOB时钟
//RCC_APB2Periph_GPIOC    GPIOC时钟
//RCC_APB2Periph_GPIOD    GPIOD时钟
}
```

