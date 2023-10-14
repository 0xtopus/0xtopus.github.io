---
title: DSP知识整理
---


推荐参考书籍：《手把手教你学DSP—基于TMS320X281x》（第3版） *顾卫钢*

# 一、DSP概述

### 硬件：

- 高速控制和信号处理
- 采用流水线操作
- 采用哈佛结构，数据存储区和程序存储区分离，数据总线和程序总线也是分开的
- 大多配有硬件乘法器和加法器，同一时钟周期内可以完成累加和累乘
- 零开销循环

### 软件：
- 显示MAC（乘加器）指令
- 精简指令集
- 专门的寻址方式

### DSP分类

按数据格式分：
- 定点
- 浮点

按用途分：
- 通用
- 专用

### DSP的应用
1. 数字电机控制
2. 数字电源供应
3. 高级传感器
4. ...


# 二、TMS320F2812

### C2000系列DSP
1. Piccolo系列
   - F2802x, F2803x/5x, F2806x
2. Delfino系列
   - F2833x/23x, C2834x, F2837xS, F2837xD 

### CPU

- 32位定点CPU
- 150MHz
- 当频率为135MHz时，核心电压1.8V；当频率为150MHz时，核心电压为1.9V；输出IO口电压为3.3V

### 内存

- 18kW RAM
- 128kW Flash

### 总线

- 3条地址总线
  - PAB：程序地址总线，**22位**
  - DRAB：数据读地址总线，32位
  - DWAB：数据写地址总线，32位
- 3条数据总线
  - DRDB：数据读数据总线，32位
  - DWDB：数据写数据总线，32位
  - PRDB：程序读数据总线，32位

### 外设功能模块
- 控制类
  - IO
  - 定时器
  - PWM

- 测量类
  - 数字：捕获、正交编码
  - 模拟：ADC

- 通讯类
  - UART
  - eCAN
  - SPI
  - McBsp

### 其他

外设中断扩展模块（PIE）
- 支持45个外部中断

片上存储器
- 128kW Flash，受到CSM（Code Security Module）保护
- 1kW的OTP型存储器（一次性可编程ROM），受到CSM的保护；
- 18kW的SARAM
  - L0和L1：两个4kW的SARAM, 受到CSM的保护
  - H0: 8kW
  - M0和M1：两个1kW的SARAM
- 56个可编程IO口
- 时钟和系统控制
- 3个32位CPU定时器
- 马达控制外设
  - 两个事件管理器
- 12位ADC，16通道

### 寻址方式

1. 直接寻址
2. 间接寻址
3. 寄存器寻址
4. 堆栈寻址
5. 数据/地址/IO空间立即寻址方式
6. 程序空间间接寻址方式

### C28x流水线
- 八级流水线，五个阶段
- 取指令，译码，读取数据，执行，写回

# 三、时钟和系统控制
![OSC&PLL](../img/DSP/OSC&PLL.png)
时钟CLKIN的来源有两种，一种是外部晶振提供，另一种是外部时钟输入。可以通过引脚<span style="text-decoration: overline">XF_PLLDIS</span>来控制选择。

- 晶振：20MHz ~ 35MHz
- 外部时钟：有PLL: 5 ~ 100MHz；无PLL: 4 ~ 150MHz

在使用对应的外设之前，需要使能它们的时钟。与外设时钟使能相关的寄存器是<u>外设时钟使能寄存器PCLKCR</u>。

SYSCLK经过分频可以得到低速外设时钟LSPCLK和高速外设时钟HSPCLK，它们互相独立，分别给高速外设和低速外设供应时钟信号。

<img src="..\img\DSP\HSPCLK_LSPCLK.png" style="zoom:75%;" />



### 看门狗电路

当八位的<u>看门狗加法计数器WDCNTR</u>达到最大值时，看门狗模块输出一个512个周期的脉冲，使系统复位。

如果不想使用看门狗，可以直接禁止看门狗。

如果启用了看门狗，当程序正常运行时，我们需要定期向WDCNTR依次顺序写入0x55和0xAA来清零，实现“喂狗”操作。

逻辑校验位是看门狗的另一个安全机制，访问<u>看门狗控制器WDCR</u>的写操作中，相应的校验位必须为“101”，否则会立即拒绝访问并引发系统复位。

### 低功耗模式

- 空闲模式：CPU进入低功耗模式，部分外设不使用时时钟选择性关闭，会自动降低频率来降低功耗，任何使能的外设中断都可以唤醒此模式。
- 暂停模式：片上所有设备停止工作
- 备用模式：关闭CPU和外设时钟，OSC和PLL正常工作

# 四、CPU定时器

x2812内部有3个32位的CPU定时器，其中只有Timer0可供用户使用，Timer1和Timer2都被系统保留。

欲使用CPU定时器来计时，需要设置两个寄存器：

- 周期寄存器 PRDH:PRD
- 分频器寄存器 TDDRH:TDDR

计数器的周期有以下两个公式：

${TIMCLK = \frac{TDDRH:TDDR + 1 }{SYSCLK} * 10^{-6}}$

${Period = (PRDH:PRD + 1) * TIMCLK}$

# 五、编程

## 程序文件

- 源程序文件：C，汇编文件
- 头文件：.h文件
- 命令文件(cmd)：对程序和数据存储空间进行分配
- 运行支持库

## 命令文件

伪指令：

- MEMORY：用来指示存储空间
  - PAGE0：程序空间
  - PAGE1：数据空间
- SECTION：用来分配到储存空间
  - 段：
    - .cinit：存放用来对全局 和静态变量初始化的函数
    - .text：存放编译C语言时产生的汇编代码
    - .econst：包含字符串常量、全局变量和静态变量的初始化和说明
    - .ebss：使用大寄存器模式时为全局变量和静态变量所预留的空间
    - .stack：为系统堆栈保留的空间，主要用于函数传递变量和为局部变量分配空间。

# 六、GPIO

总共56个可编程复用IO口，分为ABDEFG六组。复位默认为输入口。可提供4mA输入电流。

寄存器：

- GPxMUX
  - 写0，设置为数字IO口
  - 写1，功能复用
- GPxDIR
  - 写0，输入
  - 写1，输出
- GPxDAT
  - 写0，拉低输出
  - 写1，拉高输出
  - 存在一定的问题，建议使用下面三个寄存器来操作
- GPxSet：只能写1
- GPxClear：只能写1
- GPxTOGGLE：只能写1

```c
// 置位输出
GpioDataReg.GPASET.bit.GPIOA0 = 1;
// 读取输入
if(GpioDataReg.GPASET.bit.GPIOA2 == 0) {
    ...
}
```

 

# 七、中断

中断的类型：

- 触发形式
  - 软件中断
  - 硬件中断
    - 内部中断
    - 外部中断
- 能否屏蔽
  - 可屏蔽中断
  - 不可屏蔽中断

## 中断优先级

在不同组内INT1最大，在PIE同组内，INTx.1最大。

## CPU级中断

CPU中的中断由IER和INTM控制。IER控制每个可屏蔽中断，INTM相当于总闸。

## PIE中断

外设中断扩展模块（PIE），对各种中断请求源进行处理。

PIE一共支持96个中断，这些中断被分为了12个组，每组由8个中断。每个组都被反馈到 <span style="text-decoration:overline" >INT1</span> ~ <span style="text-decoration:overline" >INT12</span> 这12条中断线上的某一条。

## PIE中断向量表

DSP中各个中断服务子程序的地址储存在一片连续的RAM（大小为256x16）内，这就是中断向量表。

## 2812的三级中断系统

三级系统都要允许，某中断才会发生。

- 外设级

  - 当外设中断标志位被置1，且该外设中断为使能状态，此时外设向PIE发出中断请求；
  - <span style="color:red; font-weight:bold">外设中断标志位置位后必须<u>手动清除</u>！！！</span>

  ```c
  CpuTimer0Reg.TCR.bit.TIF = 1;	// 写1来手动清除外设寄存器中的标志位
  ```

  

- PIE级

  - 除了中断标志寄存器和中断使能寄存器，还有中断应答寄存器。
  - 同组内的中断在响应时，如果发生了其他同组中断，若中断应答寄存器没有被手动清零，后发生的中断不会被响应。<span style="color:red; font-weight:bold">中断应答寄存器需要手动清除</span>。PIE级中断的标志位不需要手动清除。

  ```c
  PieCtrl.PIEACK.bit.ACK1 = 1;
  ```

  

- CPU级

  - 当外设中断请求发送到CPU时，CPU的中断标志寄存器IFR对应位置位。这时，CPU检查IER对应的位是否置位，以及INTM是否<span style="color:red; font-weight:bold">为0</span>。

<img src="..\img\DSP\INTERRUPTION.png" style="zoom:75%;" />

## 中断函数的编写

1. 使能某个中断：

```c
void InitXXX(void) {
    ...
    CpuTimer0Regs.TCR.bit.TIE = 1;	// 使能定时器0的周期中断
    ...
}
```

2. 在主函数里：

```c
void main(void) {
    InitXXX();
    DINT;
    IER = 0x0000;
    IFR = 0x0000;
    InitPieCtrl();
    InitPieVectTable():
    // 使能PIE的中断
    PieCtrl.PIEIER1.bit.INTx7 = 1;
    IER |= M_INT1;
    EINT;
}
```

3. 在DSP28_DefaultIsr.c文件里配置中断函数

```c
interrupt void TINT0_ISR(void) {
    ...
    CpuTimer0Regs.TCR.bit.TIF = 1;
    PieCtrl.PIEACK.bit.ACK1 = 1;
    EINT;
}
```



# 八、EV模块（事件管理器）

两个事件管理器Eva和Evb，两个事件管理器的功能是一模一样的。

下面**针对Eva**来介绍事件管理器的结构：

- 2个16位通用定时器，每个定时器可以产生1路独立的PWM波。
- 3个比较单元
- 3个捕获单元
- 1个正交编码脉冲电路

Evb和Eva类似。

## 通用定时器

### 功能

1. 计时
2. 使用定时器的比较功能产生PWM波
3. 给Ev的其他模块提供时钟

### 寄存器

**GPTimer1**的寄存器们be like:

- 16位周期寄存器T1PR（带有阴影寄存器）
- 16位比较寄存器T1CMPR（带有阴影寄存器）
- 定时器计数器寄存器T1CNT
- 16位控制寄存器T1CON
- 16位通用定时器控制寄存器GPTCONA

### 通用定时器的时钟

SYSCLK  --> HSPCLK --> TCLK

$TCLK = \frac{HSPCLK}{2^{p}}$

其中，p = 1 ~ 128

### 计数方式

GPTimer1通过T1CON的TMODE1和TMODE0控制计数方式。

| TMODE1 | TMODE0 |       描述        |
| :----: | :----: | :---------------: |
|   0    |   0    |   停止/保持模式   |
|   0    |   1    | 连续增/减计数模式 |
|   1    |   0    |  连续增计数模式   |
|   1    |   1    | 定向增/减计数模式 |

对定向增/减计数模式的说明：当为定向增/减计数模式时，计时器工作在递增还是递减模式取决于引脚TDIRA的电平，高电平递增，低电平递减。

### 中断事件

- 上溢中断
- 下溢中断
- 比较中断
- 周期中断

### PWM波

要产生PWM波：

- 使能定时器的T1CON的TECMPR位为1（比较使能）
- GPTCONA的TCMPOE位为1（输出使能）

此时，T1PWM_T1CMP引脚输出PWM波。当T1CNT与T1CMPR的值相匹配时，引脚T1PWM_T1CMP电平就会发生跳变。

产生的PWM波有两种：

- 不对称（连续增计数模式）
- 对称（连续增\减计数模式）

可以配置GPTCONA的T1PIN[1:0]位控制输出的极性：

| 位1  | 位0  |    描述    |
| :--: | :--: | :--------: |
|  0   |  0   |   强制低   |
|  0   |  1   | 低电平有效 |
|  1   |  0   | 高电平有效 |
|  1   |  1   |   强制高   |

低电平有效就是T1CNT的值比T1CMPR小的时候输出高电平，其他以此类推。

定时器的周期为：

非对称模式：

$T = (T1PR + 1) * TCLK$

对称模式：

$T = (2 * T1PR) *TCLK$

## 全比较单元

Eva、Evb各有3个全比较单元（加起来总共6个），每个比较单元都可以输出2路互补的PWM波，所以Eva或者Evb可以都有能力驱动一个三相全桥电路。（当然你也可以设置输出的两路PWM完全相同）

### 寄存器

按Eva和Evb来分：

- 16位比较寄存器CMPR1~3， CMPR4~6，共6个（都有阴影寄存器）
- 16位比较控制寄存器COMCONA和COMCONB，共2个
- 16位行为控制寄存器ACTRA和ACTRB（都有阴影寄存器）

以Eva为例，输出的PWM极性可以通过ACTRA的位CMP1和CMP2控制。使能COMCONA的CENABLE和FCMPOE位时即可产生两路互补的PWM波。

每个比较寄存器都有两个对应的PWM输出引脚：比如CMPR1对应PWM1和PWM2

输出的PWM波类型和GPTimer是一样的。只是把T1CMPR改成CMPRx即可。

带有死区控制的PWM不考，不写了。

## 捕获单元

捕获单元能够捕获外部输入引脚CAPx_QEPx的电平变化。当捕获到指定的电平变化时，捕获单元就记录下定时器的时间。利用两次捕获的时间差，捕获单元就可以测量出信号的脉宽。

2812的Eva和Evb各有三个捕获单元CAP1 ~ 3和CAP4 ~ 6。每个捕获单元都有一个捕获输入

引脚，通过配置相关寄存器可以捕获输入波形的上升沿、下降沿或者同时捕获上升沿和下降沿。当引脚检测到指定的变化时，所选用的定时器的值将被捕获并锁存到对应的2级FIFO堆栈中。注意，从引脚发生变化到锁存定时器的值需要至少2个时钟周期，所以**输入信号至少要保持2个时钟周期**。

### 寄存器

- 捕获控制寄存器CAPCONA, CAPCONB
- 捕获FIFO状态寄存器CAPFIFOA， CAPFIFOB
- 两级深度的FIFO堆栈CAPFIFOx, CAPFBOTx (x = 1 ~ 6)

以Eva的CAP1为例，在堆栈为空的时候，捕获状态寄存器CAPFIFOA的CAP1FIFO状态位为00，发生一次捕获后，定时器计数寄存器T1CNT的值被存入栈顶，CAP1FIFO状态位为01，第二次捕获后，数据存入栈底，堆栈全满，CAP1FIFO状态位变为10；第三次捕获后，栈顶的数据出栈，第二次捕获的来到栈顶，第三次捕获的来到栈底。如果没有读取第一次捕获的值（第一次捕获的值丢失），则CAP1FIFO状态位变为11；否则如果在第三次捕获前已经读取了第一次捕获的值，则CAP1FIFO状态位仍为10.



### 中断

以Eva为例，捕获单元1具有捕获中断CAP1INT，捕获单元2有CAP2INT，捕获单元3有CAP3INT...

中断发生的条件：

1. 捕获单元捕获到信号发生指定的变化
2. 此时CAPxFIFO不为0（堆栈非空）

则此时中断标志位被置位。

和中断相关的寄存器：

- 中断标志寄存器EVAIFRC，EVBIFRC
- 中断屏蔽寄存器EVAIMRC和EVBIMRC（用来使能中断的）

## 正交编码电路

每个事件管理器都有一个正交编码电路（QEP电路），光电码盘输出的两路正交编码信号从两个输入引脚输入到QEP电路，在通过两个QEP电路的译码器对正交编码信号进行译码，最后就能得到电机转子的转速、旋转方向、旋转位置等信息。



```c
/*
 * 自己写的事件管理器Init函数
 * 配置了全比较单元输出PWM波和捕获单元捕获上升下降沿
 */
void InitEv(void)
{
	/*
	 * 配置GP Timer2
	 */
	EvaRegs.T2CON.bit.TENABLE = 0; 	// 暂时禁止t2计数
	EvaRegs.T2CON.bit.TPS = 0;			// 输入计数时钟为HSPCLK = 75MHz
	EvaRegs.T2CON.bit.TMODE = 2; 		// GP Timer2连续增计数模式
	EvaRegs.T2CON.bit.TCLKS10 = 0; 	// 选择内部时钟T2CLK

	EvaRegs.T2CNT = 0x0000;	// Clear the counter for GP timer 2
	EvaRegs.T2PR = 0x3A97;		// 频率为5kHz，PR = 14999

	/*
	 * GP Timer2周期中断配置
	*/
	EvaRegs.EVAIMRB.bit.T2PINT = 1;		// 使能GP Timer2周期中断
	EvaRegs.EVAIFRB.bit.T2PINT = 1;			// 复位GP Timer2周期中断标志位


	/*
	 * 配置GP Timer1
	 */
	EvaRegs.T1CON.bit.TENABLE = 0; 	// 暂时禁止t1计数
	EvaRegs.T1CON.bit.TPS = 0;			// 输入计数时钟为HSPCLK = 75MHz
	EvaRegs.T1CON.bit.TMODE = 2; 		// GP Timer1连续增计数模式
	EvaRegs.T1CON.bit.TCLKS10 = 0; 	// 选择内部时钟T1CLK

	EvaRegs.T1CNT = 0x0000;		// Clear the counter for GP timer 1
	EvaRegs.T1PR = 0x3A97;		// 频率为5kHz，PR = 14999

	/*
	 * 配置全比较模块
	 */
	EvaRegs.COMCONA.bit.CENABLE = 1;		// 使能EVA的全比较操作
	EvaRegs.COMCONA.bit.FCOMPOE = 1;	// 使能比较输出

	EvaRegs.COMCONA.bit.CLD = 0;				// 比较寄存器重载条件为下溢中断

	EvaRegs.CMPR1 = 0x1D4C;	// 默认占空比为50%
	EvaRegs.CMPR2 = 0x1D4C;	// 默认占空比为50%
	EvaRegs.CMPR3 = 0x1D4C;	// 默认占空比为50%
	EvaRegs.ACTR.all = 0;			// 全部强制低

	/*
	 * GP Timer1下溢中断配置
	 */
	EvaRegs.EVAIMRA.bit.T1UFINT = 1;		// 使能GP Timer1下溢中断
	EvaRegs.EVAIFRA.bit.T1UFINT = 1;		// 复位GP Timer1下溢中断标志位
    
    
    EvaRegs.CAPFIFO.all = 0; // 初始化CAPFIFOA

	EvaRegs.CAPCON.bit.CAPRES = 0; 			// 清零捕获寄存器
	EvaRegs.CAPCON.bit.CAPQEPN = 1; 		// 使能捕获单元1,2
	EvaRegs.CAPCON.bit.CAP3EN = 1;			// 使能捕获单元3
	EvaRegs.CAPCON.bit.CAP3TSEL = 1;		// 捕获单元3时基为timer1
	EvaRegs.CAPCON.bit.CAP12TSEL = 1;		// 捕获单元1,2时基为timer1

	EvaRegs.CAPCON.bit.CAP1EDGE = 0x3;	// 捕获单元1捕获边沿为上升下降沿
	EvaRegs.CAPCON.bit.CAP2EDGE = 0x3;	// 捕获单元2捕获边沿为上升下降沿
	EvaRegs.CAPCON.bit.CAP3EDGE = 0x3;	// 捕获单元3捕获边沿为上升下降沿

	EvaRegs.EVAIMRC.bit.CAP1INT = 1;		// 使能捕获单元1中断
	EvaRegs.EVAIMRC.bit.CAP2INT = 1;		// 使能捕获单元2中断
	EvaRegs.EVAIMRC.bit.CAP3INT = 1;		// 使能捕获单元3中断
	EvaRegs.EVAIFRC.all = 0x7;		// 复位捕获单元中断标志位

	EvaRegs.T1CON.bit.TENABLE = 1; 	// 使能t1计数

}
```



# 九、ADC

- 2812的ADC分辨率为12bit，具有流水线结构，具有16个通道，分为2组：一组为ADCINA0 ~ ADCINA7，另一组为ADCINB0 ~ ADCINB7，分别对应两个采样保持器。虽然通道很多，但是转换器只有一个，所以要对各通道进行排序转换。

- ADC模块的时钟最高频率为25MHz，最高采样率为12.5MSPS（sample per second）。
- ADC电压的采样范围是<u>0 ~ 3 V</u>
- ADC模块对一个序列的通道开始转换必须要有一个启动信号来触发，当信号到来时，相应的序列发生器就开始对内部预先指定的通道进行转换。
- ADC有两种工作模式：
  - 两个独立的8通道
  - 级联16个通道
- ADC共有16个结果寄存器来存储转换的数值。

## 工作方式

通过16位的ADC输入通道选择序列控制寄存器ADCCHSELSEQx（x=1, 2, 3, 4）可以控制ADC通道的<u>转换顺序</u>。

每个ADCCHSELSEQx被分为了4段，每段其名为：CONVxx（x = 00 ~ 15）。

- 当ADC工作在**双序列发生器模式**下时，序列发生器SEQ1使用ADCCHSELSEQ1和2，每个CONVx可以选择的通道为ADCINA0 ~ 7；SEQ2使用ADCCHSELSEQ3和4，每个CONVx可以选择的通道为ADCINB0 ~ 7;
- 当ADC工作在**级联模式**下时则都可自由选择。

ADC转换的<u>通道数量</u>由最大通道转换寄存器ADCMAXCONV决定。

- 当工作在双序列发生器模式下时，该寄存器的低三位[0:2]所代表的**二进制数 + 1**决定SEQ1序列发生器转换的通道数量；[4:6]位决定SEQ2转换的通道数量。
- 工作在级联模式下时，低四位[0:3]决定转换通道的数量，为其代表的**二进制数 + 1**

ADC还有顺序采样和并发采样两种采样方式，所以排列组合一下，就有：

- 双通道顺序采样
- 级联顺序采样
- 双通道并发采样
- 级联并发采样

四种模式。

下面是一个配置双序列顺序采样的例子：

```c
	AdcRegs.ADCTRL1.bit.SEQ_CASC = 0; //独立双排序模式
	AdcRegs.ADCTRL1.bit.CONT_RUN = 0; //启动停止模式

	// 级联双排序的排序器配置
	AdcRegs.MAX_CONV.bit.MAX_CONV = 0x0032; // B组采样通道为4，A组采样通道为3
	AdcRegs.CHSELSEQ1.bit.CONV00 = 0x0; // 选择模拟通道ADCINA0
	AdcRegs.CHSELSEQ1.bit.CONV01 = 0x1; // 选择模拟通道ADCINA1
	AdcRegs.CHSELSEQ1.bit.CONV02 = 0x2; // 选择模拟通道ADCINA2

	AdcRegs.CHSELSEQ3.bit.CONV08 = 0xB; // 选择模拟通道ADCINB3
	AdcRegs.CHSELSEQ3.bit.CONV08 = 0xC; // 选择模拟通道ADCINB4	
	AdcRegs.CHSELSEQ3.bit.CONV08 = 0xD; // 选择模拟通道ADCINB5
	AdcRegs.CHSELSEQ3.bit.CONV08 = 0xE; // 选择模拟通道ADCINB6
```

序列发生器的工作模式有两种：启停模式和连续转换模式。由ADCTRL1的CONT_RUN位控制。在连续转换模式下，当一次序列转换结束后，会自动从该序列头部开始重新进行转换。当工作在启停模式下的时候，序列发生器完成一次转换后会停留在最后状态，需要先**手动复位**后才能在下一次SOC（start of conversion）信号到来时才会重新开始转换。

```c
AdcRegs.ADCTRL2.bit.RST_SEQ1 = 1;  // 手动复位序列发生器
```

## 结果读取

ADC的采样结果存储在16个16位的ADC结果寄存器里，左对齐，结果占据高12位，所以计算的时候需要右移4位。

$(ADResult >> 4) = \frac{(V_{i}  - ADCCLO)}{3 V} *4095$

ADCLO一般接模拟地，所以为0：

```c
res = (AdcRegs.RESULT0 >> 4) / 4095 * 3;
```



# 十、SPI

串行外设接口，是一种**低速同步串行通信接口**，而SCI是异步通信。

- 异步通信和同步通信的区别在于**收发双方是否使用同一个时钟信号来控制数据收、发移位操作**。

## 工作方式

SPI总线至少包含一根时钟线和数据线。2812使用4线制SPI，以主从方式进行工作，全双工通信。通信系统中通常有一个主设备和多个从设备。

|                     线路名称                     |           功能           |
| :----------------------------------------------: | :----------------------: |
|                       SCK                        | 串行时钟线（主设备控制） |
|                       MISO                       |    主机输入/从机输出     |
|                       MOSI                       |    主机输出/从机输入     |
| <span style="text-decoration:overline">CS</span> | 是低电平有效的从机选择线 |

SPI的波特率 = SPICLK <= LSPCLK/4。实际使用时要确保小于从机的最大允许速率。

<img src="..\img\DSP\spi.png" alt="spi" style="zoom:75%;" />

