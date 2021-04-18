## I2C简介
### 硬件层
+ 支持总线，可多机通信，总线需要拉高，当设备空闲时，输出高阻态
+ 它是真正意义上的多主设备总线
+ SDA数据线，SCL时钟线
+ 为了防止数据冲突，会由仲裁的方式决定总线的占用
+ 链接到总线的IC数量受到总线的最大电容400pf限制
+ I2C总线（SDA，SCL）内部都使用漏极开路驱动器（开漏驱动），因此SDA和SCL 可以被拉低为低电平，但是不能被驱动为高电平，所以每条线上都要使用一个上拉电阻，默认情况下将其保持在高电平；
### 协议层
+ 开始信号：SCL为高，SDA被拉低，然后SCL被拉低。
+ 应答信号：在第9个时钟信号，如果从设备发送应答信号ACK，则SDA会被拉低；若没有应答信号NACK，则SDA会输出为高电平，这过程会引起主设备发生重启或者停止；
+ 停止信号：SCL为高，SDA被拉高，然后SCL被拉低。
+ 写数据：起始信号+等待响应+地址（附带读写方向，0表示写数据）+ 等待回复+一个字节的数据+等待回复...发送停止信号
+ 读数据：起始信号+等待响应+地址（附带读写方向，1表示读数据）+ 等待回复+一个字节的数据+发送应答...发送非应答，停止接收
+ 读写复合：包括两段起始信号。起始信号+等待响应+地址（写）+等待回复+数据（从机内部地址）+起始信号......
## STM32中的驱动



## HC32中的驱动
HC32中I2C_CR.sta 标志位置1为I2C开始，随后通过查看I2C_CR.si的值，来进行判断，每操作一步，将I2C_CR.si置零，收到回复后I2C_CR.si会被自动置为1。通信时许中，读写反转设计的很巧妙，I2C_STAT的值为0x08 或 0x10都为读和写的起始返回值。 	 
```c
/************************************************
函数名称 ： my8Addr_I2C_MasterReadData
功    能 ： 读取lis2dh12tr加速度传感器三轴的值
参    数 ： u8DevAddr，从机地址，u16Addr，寄存器起始地址
            *pu8Data，缓存读取数据，u32Len，读取数据字节长度
返 回 值 ： en_result_t，正常情况下返回OK
作    者 ： wang
*************************************************/
en_result_t my8Addr_I2C_MasterReadData(uint8_t u8DevAddr, uint8_t u8Addr, uint8_t *pu8Data, uint32_t u32Len)
{
    en_result_t enRet = Error;
    uint8_t u8i = 0, u8State;
    I2C_SetFunc(I2cStart_En);

    while (1)
    {
        while (0 == I2C_GetIrq())
        {
        }
        u8State = I2C_GetState();
        switch (u8State)
        {
        case 0x08: //跳转条件：第一次开始信号
            I2C_ClearFunc(I2cStart_En);
            I2C_WriteByte(u8DevAddr << 1);
            break;
        case 0x18: //跳转条件：发送ADDR+W信号，接收到ACK
            I2C_WriteByte(u8Addr);//写入需要读取的寄存器地址
            break;
        case 0x28: //跳转条件：调用I2C_WriteByte得到ACK
            I2C_SetFunc(I2cStart_En);//准备第二次开始
            break;
        case 0x10: //跳转条件：第二次开始信号，一般用于读写反转
            I2C_ClearFunc(I2cStart_En);
            I2C_WriteByte((u8DevAddr << 1) | 0x01); //从机地址发送OK
            break;
        case 0x40: //跳转条件：发送ADDR+R信号，得到ACK

            if (u32Len > 1)
            {
                I2C_SetFunc(I2cAck_En);
            }

            break;
        case 0x50: //跳转条件：接收到数据，上次返回ACK
            pu8Data[u8i++] = I2C_ReadByte();
            if (u8i == u32Len - 1) //倒数第二字节ACK清掉，最后字节不反馈ACK
            {
                I2C_ClearFunc(I2cAck_En);
            }
            break;
        case 0x58: //跳转条件：接收到数据，上次返回NACK
            pu8Data[u8i++] = I2C_ReadByte();
            I2C_SetFunc(I2cStop_En);
            break;
        case 0x38:
            I2C_SetFunc(I2cStart_En);
            break;
        case 0x48:
            I2C_SetFunc(I2cStop_En);
            I2C_SetFunc(I2cStart_En);
            break;
        default:
            I2C_SetFunc(I2cStart_En); //其他错误状态，重新发送起始条件
            break;
        }
        I2C_ClearIrq();
        if (u8i == u32Len)
        {
            break;
        }
    }
    enRet = Ok;
    return enRet;
}


/************************************************
函数名称 ： my8Addr_I2C_MasterWriteData
功    能 ： 对lis2dh12tr加速度传感器的一个指定地址写入数据，
            若从机支持地址自动增加，则可实现连续写入数据，
            否则只会对同一个地址进行连续写入
参    数 ： u8DevAddr，从机地址
            u16Addr，寄存器起始地址
            *pu8Data，缓存写入取数据，可以传入数组首地址
            u32Len，写入数据字节长度
返 回 值 ： en_result_t，正常情况下返回OK
作    者 ： wang
*************************************************/
en_result_t my8Addr_I2C_MasterWriteData(uint8_t u8DevAddr,uint8_t u8Addr,uint8_t *pu8Data,uint32_t u32Len)
{
    en_result_t enRet = Error;
    uint8_t u8i=0,u8State;
    I2C_SetFunc(I2cStart_En);
	while(1)
	{
		while(0 == I2C_GetIrq())
		{;}
		u8State = I2C_GetState();
		switch(u8State)
		{
			case 0x08:
				I2C_ClearFunc(I2cStart_En);
				I2C_WriteByte(u8DevAddr);//从设备地址发送
				break;
			case 0x18:
				I2C_WriteByte(u8Addr);//从设备内存地址发送
				break;
			case 0x28://跳转条件：调用I2C_WriteByte得到ACK	
				I2C_WriteByte(pu8Data[u8i++]);
				break;
			case 0x20:
			case 0x38:
				I2C_SetFunc(I2cStart_En);
				break;
			case 0x30:
				I2C_SetFunc(I2cStop_En);
				break;
			default:
				break;
		}			
		if(u8i>=u32Len)
		{
			I2C_SetFunc(I2cStop_En);//此顺序不能调换，出停止条件
			I2C_ClearIrq();
			break;
		}
		I2C_ClearIrq();			
	}
    enRet = Ok;
    return enRet;
}
```