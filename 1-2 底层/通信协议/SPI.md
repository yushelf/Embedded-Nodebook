## SPI简介
摩托罗拉公司提出，高速全双工的通信总线
### 硬件层
+   四根线，一个主机可接多个从机。
+   SS：片选，置0为选中，开始信号，置1停止。
+   SCK：时钟信号，主机产生，频率取决于主机，stm32为f/2
+   MOSI:主机数据输出
+   MISO:主机数据接收
### 协议层
+   SS线为低电平的时候，数据有效，开始传输。
+   数据在SCLK上升时改变，在SCLK下降时 采样
+   SPI有四种工作模式：
	| SPI模式   | CPOL    | CPHA    |空闲时SCK时钟 |采样时刻|
	| ------------ | ---------- | --------- | --------------------- | ------------|
	| 0                 |0               | 0             | 低电平                 |第一个奇数边沿 |
	| 1                 |0               | 1             |低电平                  |第二个奇数边沿 |
	| 2                 | 1              | 0             | 高电平                 | 第二个奇数边沿 |
	| 3                 | 1               |1             |高电平                  | 第一个奇数边沿 |
	+ 模式0与模式3使用较多
	+ 应该仔细检查微控制器数据手册中包含的模式表，以确保一切正常。

### 特性
#### 时钟频率
SPI总线上的主机必须在通信开始时候配置并生成相应的时钟信号。在每个SPI时钟周期内，都会发生全双工数据传输。主机在MOSI线上发送一位数据，从机读取它，而从机在MISO线上发送一位数据，主机读取它。

就算只进行单向的数据传输，也要保持这样的顺序。这就意味着无论接收任何数据，必须实际发送一些东西！在这种情况下，我们称其为虚拟数据；

从理论上讲，只要实际可行，时钟速率就可以是您想要的任何速率，当然这个速率受限于每个系统能提供多大的系统时钟频率，以及最大的SPI传输速率。
#### 多从机模式
在数字通信世界中，在设备信号（总线信号或中断信号）以串行的方式从一 个设备依次传到下一个设备，不断循环直到数据到达目标设备的方式被称为菊花链。

1. 菊花链的最大缺点是因为是信号串行传输，所以一旦数据链路中的某设备发生故障的时候，它下面优先级较低的设备就不可能得到服务了；
2. 另一方面，距离主机越远的从机，获得服务的优先级越低，所以需要安排好从机的优先级，并且设置总线检测器，如果某个从机超时，则对该从机进行短路，防止单个从机损坏造成整个链路崩溃的情况；


## STM32中的驱动
`STM32的cubemx自动生成的HAL库代码，比较简单，截取了其中一部分`

```c
static void MX_SPI1_Init(void)
{
    hspi1.Instance = SPI1;
    hspi1.Init.Mode = SPI_MODE_MASTER;    //主机模式
    hspi1.Init.Direction = SPI_DIRECTION_2LINES; //全双工
    hspi1.Init.DataSize = SPI_DATASIZE_8BIT;  //数据位为8位
    hspi1.Init.CLKPolarity = SPI_POLARITY_LOW;  //CPOL=0
    hspi1.Init.CLKPhase = SPI_PHASE_1EDGE;   //CPHA为数据线的第一个变化沿
    hspi1.Init.NSS = SPI_NSS_SOFT;     //软件控制NSS
    hspi1.Init.BaudRatePrescaler = SPI_BAUDRATEPRESCALER_2;//2分频，32M/2=16MHz
    hspi1.Init.FirstBit = SPI_FIRSTBIT_MSB;   //最高位先发送
    hspi1.Init.TIMode = SPI_TIMODE_DISABLE;   //TIMODE模式关闭
    hspi1.Init.CRCCalculation = SPI_CRCCALCULATION_DISABLE;//CRC关闭
    hspi1.Init.CRCPolynomial = 10;     //默认值，无效
    if (HAL_SPI_Init(&hspi1) != HAL_OK)    //初始化
    {
        _Error_Handler(__FILE__, __LINE__);
    }
}
    
//发送数据
HAL_StatusTypeDef  
HAL_SPI_Transmit(SPI_HandleTypeDef *hspi, 
                 uint8_t *pData, 
                 uint16_t Size, 
                 uint32_t Timeout);
//接收数据
HAL_StatusTypeDef  
HAL_SPI_Receive(SPI_HandleTypeDef *hspi, 
                uint8_t *pData, 
                uint16_t Size, 
                uint32_t Timeout);
```


## HC32中的驱动