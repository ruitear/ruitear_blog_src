---
title: NR PDSCH资源分配
date: 2021-06-25 23:55:53
---

##### [](#1-PDSCH频域资源分配)1. [PDSCH频域资源分配](#PDSCH时域资源分配)

PDSCH频域资源分配方式有两种，称为**type0**和**type1**，**支持动态和静态配置**；动态配置由DCI进行指示，而静态配置通过IE pdsch-Config中参数resourceAllocation指示，如下图所示；
![pdsch-config-1](http://1.116.145.136/NR/pdsch-config-1.png)

###### [](#1-1-终端如何获取频域资源分配方式的信息)1.1 [终端如何获取频域资源分配方式的信息](#终端如何获取频域资源分配方式的信息)

- 如果终端通过**DCI 1_0**接收调度，那么只能是**Type 1**；

- 如果终端通过**DCI 1_1**接收调度，需要参考RRC参数配置决定。通过基站在**pdsch-Config**中配置**resourceAllocation**来指示UE采用哪种频域资源分配方式，如上表PDSCH-Config所示。如果配置的是**dynamicSwitch**，UE需要借助DCI中Frequency domain resource assignment的MSB这个bit来判定这次调度是type0还是type1。如果是resourceAllocationType0或者resourceAllocationType1，配的什么就用什么；

###### [](#1-2-RB资源起始和RBG-size的确定)1.2 [RB资源起始和RBG size的确定](#RB资源起始和RBG size的确定)

- **RB起始的确定**：UE在检测到预期用于UE的PDCCH时将首先确定下行载波BWP，然后确定BWP内的资源分配。如果在DCI中未配置BWP指示符字段或者UE不支持由DCI改变激活BWP，那么在UE激活的BWP内确定用于下行type0和type1资源分配的RB索引。如果DCI中配置了BWP指示符字段并且UE支持由DCI改变激活BWP，则DCI中BWP指示符字段的值指示的UE的BWP内确定用于下行type0和type1资源分配的RB索引。对于PDCCH CSS中由DCI_1_0调度的PDSCH，无论哪个激活BWP，RB编号从接收DCI的CORESET的最低RB开始；否则，RB编号从所确定的下行BWP中的最低RB开始。

- **RBG Size的确定**：其由IE PDSCH-Config中参数rbg-Size和当前的BWP带宽决定，如下图所示：rbg-size确定配置类型是configuration 1还是configuration 2，然后再根据当前BWP的大小确认RBG Size。

![normal_rbg_size](http://1.116.145.136/NR/normal_rbg_size.png)

RBG个数的确定在协议中有如下的定义：![RBG-num-1.png](http://1.116.145.136/NR/RBG-num-1.png)

###### [](#1-3-type0和type1的频域资源分配)1.3 [type0和type1的频域资源分配](#type0和type1的频域资源分配)

- **Type0**：是一种频域资源非连续分配的方式，相对灵活：使用bitmap来指示用于分配给终端的频域资源，1表示分配，0表示未分配。每个bit代表一个RBG，**RBG是的大小**和两个因素相关，一个是**当前BWP的大小**，二是**参数rbg-Size**是Configuration 1还是Configuration 2。RBG的index在频域上是从BWP的low frequency向high frequency来计数；

- **Type1**：是一种频域资源连续分配的方式：使用RIV的方式告知终端所分配RB的起始位置RB_start和分配了多少个连续的RB，即L_RB；【38214的5.1.2.2.2有RIV计算的过程】。**RB_start和L_RB的使用范围**：对于DCI 1_0 common search space的情况，此时使用CORESET0的大小 (if CORESET0 configured)或者初始BWP大小(if CORESET0 not configured)；对于其他情况使用当前BWP的大小。

终端在收到DCI后，通过其中的的Frequency domain resource assignment这个字段根据结合当前的配置类型，来选择通过Bitmap还是RIV的方式计算到底分配了哪些RB资源。DCI中的Frequency domain resource assignment实际是一串指示rbg或者RB的bitmap或者RIV值。协议中关于Frequency domain resource assignment的**描述如下图所示**，
![pdsch-rbg_alloc.png](http://1.116.145.136/NR/pdsch-rbg_alloc.png)

##### [](#2-PDSCH时域资源分配)2. [PDSCH时域资源分配](#PDSCH时域资源分配)

终端需要获取的PDSCH时域资源信息主要有3个，分别是：**K0**、**起始symbol和长度**和**Mapping TypeA/B**；其中：K0是终端接收到PDCCH DCI调度信息到真正接收PDSCH数据的时间间隔，单位为slot；起始symbol和长度指的是终端需要知道在所调度的slot内那些symbol是属于分配给自己的；Mapping TypeA/B是slot内symbol映射的类型。
![pdsch_mapping_type](http://1.116.145.136/NR/pdsch_mapping_type.png)

###### [](#2-1-终端如何获取配置K0、起始symbol和长度和Mapping-TypeA-B)2.1 [终端如何获取配置K0、起始symbol和长度和Mapping TypeA/B](#终端如何获取配置K0、起始symbol和长度和Mapping TypeA/B)

- **通过DCI_1_0/DCI_1_1获取** ：在DCI_1_1中，有一个Time domain resource assignment字段, 有0、1、2、3或4bit信息，确定表5.1.2.1.1-2/3/4/5的行索引Row index，协议中的描述如下：
> 
Time domain resource assignment – 0, 1, 2, 3, or 4 bits as defined in Clause 5.1.2.1 of [6, TS 38.214]. The bitwidth for this field is determined as log2 (I) bits, where I is the number of entries in the higher layer parameter pdsch-TimeDomainAllocationList if the higher layer parameter is configured; otherwise I is the number of entries in the default table.

协议38214中表5.1.2.1.1-2/3/4/5给出了PDSCH的时域信息配置表，可以通过row index去获取，表格信息如下图所示：
![38214_5.1.2.1.1-23.png](http://1.116.145.136/NR/38214_5.1.2.1.1-23.png)![38214_5.1.2.1.1-45.png](http://1.116.145.136/NR/38214_5.1.2.1.1-45.png)

- 通过pdsch-ConfigCommon或pdsch-Config配置的pdsch-TimeDomainAllocationList获取时域信息，这个参数组中包含多组{K0，mappingType, startSymbolAndLength}的参数组，其中startSymbolAndLength是以SLIV的形式给出的，可以计算出Start Symbol和Length，下图是配置的示例：

![38214_5.1.2.1.1-45.png](http://1.116.145.136/NR/pdsch_config_setup.png)SLIV具体计算过程在38214-5.1.2.1中介绍，通过TimeDomainAllocationList获取时域信息参数组，结合SLIV的计算公式和有效的SLIV的约束表38_214_5_1_2_1-1，可以计算出起始符号和长度分别是多少。
![38_214_5_1_2_1-1.png](http://1.116.145.136/NR/38_214_5_1_2_1-1.png)

例如：上图中SLIV是54，其中映射类型是typeA，使用normal cp在，遍历可以计算得到S=1，L=12。
SLIV对应的SL表格，可通过如下链接查看获取[5G-SLIV](https://www.sharetechnote.com/html/5G/5G_SLIV.html).

###### [](#2-2-什么时候使用DCI或者RRC配置获取时域pdsch信息)2.2 [什么时候使用DCI或者RRC配置获取时域pdsch信息](#什么时候使用DCI或者RRC配置获取时域pdsch信息)

PDSCH的时域资源是根据DCI format 1_0/ DCI format 1_1中Time domain resource assignment字段进行确定。其中Time domain resource assignment的bits数取决于RNTI、PDCCH搜索空间以及高层是否在IE pdsch-ConfigCommon或pdsch-Config中配置pdsch-TimeDomainAllocationList相关；如果pdsch-ConfigCommon或pdsch-Config中配置了pdsch-TimeDomainAllocationList，则UE采用pdsch-TimeDomainAllocationList进行时域资源的确定，否则UE根据RNTI、PDCCH搜索空间等信息采用默认的时域资源表进行查表也就是38214中表5.1.2.1.1-2/3/4/5。通过字段Time domain resource assignment的值m用于确定时域资源分配表中的行索引m+1。
![pdsch_common.png](http://1.116.145.136/NR/pdsch_common.png)

pdsch-ConfigCommon中的配置适用于c-rnti或者cs-rnti加扰的pdcch，不适用于38214表5.1.2.1.1-1中默认值的coreset#0；
![pdsch-TimeDomainAllocationList.png](http://1.116.145.136/NR/pdsch-TimeDomainAllocationList.png)

- **如果pdsch-TimeDomainAllocationList被配置**，那么DCI中Time domain resource assignment的字段宽度就由他确定，DCI中value 0,对应pdsch-TimeDomainAllocationList中的第一个元素，value 2对应第二个，一次类推，其中maxNrofDL-Allocations为16，也就是Time domain resource assignment最大是4bit。

- **如果pdsch-TimeDomainAllocationList没有被配置**，那么Time domain resource assignment的bit数由38214表5.1.2.1.1-2/3/4/5确认，默认是4bit。

> 
例1：如果UE在PDCCH的Type0A common搜索空间中搜到了由SI-RNTI加扰的PDCCH，S/PBCH block and CORESET multiplexing pattern 是2，那么查表38214-5.1.2.1.1-1，如下，可以得到需要查default B的表，也就是
表38214-5.1.2.1.1-4，根据DCI的Time domain resource assignment确定Row Index，然后找到对应的PDSCH时域资源信息。

![38214-5.1.2.1.1-1.png](http://1.116.145.136/NR/38214-5.1.2.1.1-1.png)

> 
例2：如果UE在UE specific 搜索空间中搜到了由C-RNTI加扰的PDCCH，其中pdsch-ConfigCommon 包含 pdsch-TimeDomainAllocationList，pdsch-Config中包含了 pdsch-TimeDomainAllocationList，那么pdsch-Config中的pdsch-TimeDomainAllocationList的配置将覆盖pdsch-ConfigCommon中的。从表38214-5.1.2.1.1-1中可以得知，DCI中Time domain resource assignment字段值指示的是pdsch-Config中的pdsch-TimeDomainAllocationList的索引，从而得到PDSCH时域资源{k0，mapping type， sliv}信息对，通过其中SLIV通过上述的反推方式可以计算出psdch的起始符号和符号长度。

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
