---
title: 5G基础-上行调度的BSR
date: 2021-05-20 23:38:29
---

BSR是一种从UE到网络的MAC CE；它承载有关UE缓冲区中有多少数据要发送出去的信息，用来申请上行带调度资源通知基站需要多少资源；

##### [](#1-BSR的配置和内容)1. [BSR的配置和内容](#BSR的配置和内容)

UE可以建立多个无线承载，每个承载对应一个逻辑信道，如果每个逻辑信道都上报一个BSR，会产生大量的开销，基于这种考虑就引入了逻辑信道组的概念LCG，将逻辑信道放到逻辑信道组中，UE基于LCG来上报BSR，而不是为每个逻辑信道上报BSR，这样把相似的逻辑信道放到一个LCG中，优化了BSR上报的机制。如何划分LCG是基站根基算法实现，这里不讨论。逻辑信道的配置在LogicalChannelConfig->logicalChannelGroup中配置。

![LogicalChannelConfig](/images/NR/LogicalChannelConfig.png)

在logicalChannelConfig中的参数logicalChannelSR-Mask需要注意一下，这个参数仅针对常规BSR有效，当UE触发一个常规BSR，且已经配置了上行授权同时该逻辑信道的logicalChannelSR-Mask为true，那么就不需要发送SR。如果logicalChannelSR-Mask为false，则需要发送SR；对应到每个MAC实体的bsr-config如下图所示：

![bsr-config](/images/NR/bsr-config.png)从配置中可以看到BSR-Config有几个定时器相关的参数，分别是：

- [1] **retxBSR定时器**，重传定时器是为了避免UE一直发BSR而得不到UL Grant的情况；

- [2] **periodicBSR定时器**，周期性检查UE的BSR，避免由于其他条件不满足导致UE的BSR得不到调度的情况；

- [3] **logicalChannelSR-DelayTimer**，表示在logicalChannelSR-DelayTimerApplied的启用的逻辑信道的SR传输延时定时器；如果逻辑信道配置了logicalChannelSR-DelayTimerApplied，在触发常规BSR时，会启动或者重启logicalChannelSR-DelayTimer；如果没有配置logicalChannelSR-DelayTimerApplied，在没有上行调度资源来传输新传数据时会触发SR；

BSR通过MAC层的MAC CE上报，NR定义了有4种类型的BSR MAC CE，分别是: 短BSR/短截断BSR，长BSR/长截断BSR。一个BSR MAC CE与一个对应一个MAC subheader，也就是对应的LCID值，对应关系如下表所示：

![bsr-lcid](/images/NR/bsr-lcid.png)需要注意的是LCID和LCG不是一个概念，LCID是MAC PDU对应的逻辑信道号。

短BSR和短截断BSR，只上报一个LCG的BSR，长度固定为8bit，并有一个长度为3bit的LCG ID字段和一个长度为5bit的buffer Size字段组成。如下图所示：

![pusch_bsr](/images/NR/short-bsr.png)长BSR/长截断BSR可以上报一个或者多个LCG的BSR，长度不固定，如下图所示，LCG ID有3bit的字段，最多可以上报8个LCG，其值对应LogicalChannelConfig->logicalChannelGroup；图中的LCG序号标识对应编号的逻辑信道是否存在buffer size，如果改字段设置为1，则表明该编号对应的逻辑信道有BSR；否则没有；其中buffer size，长度8bit，是LCG所有逻辑信道的RLC和PDCP中剩余的可用于上行的有效传输数据综合，以byte为单位，不计算RLC和MAC的数据头。

![pusch_bsr](/images/NR/long-bsr.png)长/短BSR的buffersize分别是**8bit**和**5bit**，那么他们要给基站的参考数据量有下标定义给出一个大概的范围，表中的buffer size value作为基站分配合适上行资源的参考，并不表示一定能给UE分出来这么多。
![pusch_bsr](/images/NR/buffer_size_for_5bit.png)![pusch_bsr](/images/NR/NR_MAC_CE_BSR_8bit_01.png)![pusch_bsr](/images/NR/NR_MAC_CE_BSR_8bit_02.png)

##### [](#2-BSR分类)2. [BSR分类](BSR分类)

协议中定义了三种类型的BSR：分别是Regular BSR，Periodic BSR和Padding BSR。三种BSR场景下适配长短BSR关系主要根据要发的LCG数量和响应的传输bit决定，对应关系如下图所示：

![pusch_bsr](http://1.116.145.136/NR/bsr_fasong.png)

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
