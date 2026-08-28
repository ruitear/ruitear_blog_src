---
title: 5G基础--SR
date: 2021-05-16 21:29:28
---

##### [](#1-SR-Scheduling-Request)1. [SR(Scheduling Request)](#SR(Scheduling_Request))

SR是UE的调度请求，用在UE像基站申请上行数据的新传调度,通过PUCCH传输，有些格式的PUCCH格式可以携带SR有些则不行。不是每次都能不一定能申请到PUCCH资源，只有UE处于RRC Connected状态且保持上行同步的UE才会发送SR；
![SR](http://1.116.145.136/NR/SR_0.png)

##### [](#2-SR的配置)2. SR的配置

在基站中SR的配置在SchedulingRequestResourceId IE中定义了SR资源的数量，最大8个SR资源，对应的SchedulingRequestResourceId就是0-7 ；
![SchedulingRequestId](http://1.116.145.136/NR/SchedulingRequestId_0.png)
MAC的配置中， 逻辑信道可以分别关联SR配置，一个MAC实体可以配置0个、1个或者多个SR配置，不同的逻辑信道可以关联相同的schedulingRequestID，**如果没有给相应的逻辑信道配置SR**，当该逻辑信道有数据发送时只能通过随机接入获得上行调度。
一个mac实体通过MAC-CellGroupConfig->**schedulingRequestConfig**->schedulingRequestToAddModList配置一个SR列表。其中每个SR配置对应一个schedulingRequestConfig；
![SchedulingRequestConfig](http://1.116.145.136/NR/SchedulingRequestConfig.jpg)

一个SR配置(SchedulingRequestConfig)包含了分布在不同bwp或者小区中用于传输SR的一组PUCCH资源，也就是SR资源，目前SR只支持PUCCH format0或者PUCCH format1. 从协议38.331的描述可以看到一个SchedulingRequestConfig中对应一组SchedulingRequestResourceConfig，也就是一个SR配置可以对应多个SR资源，他们实际使用是分布在不同的小区或者BWP上。在特定逻辑信道上，每个BWP最多配置1个用于SR的PUCCH资源。

上行BWP通过BWP-UplinkDedicated->pucch-Config->schedulingRequestResourceToAddModList配置一个SR资源表，每个SR资源对应一个SchedulingRequestResourceConfig，这个SR资源有一个schedulingRequestID指示它被哪个SR配置使用，**schedulingRequestID** 是区分不同的SR配置的唯一值，关联了**sr-ProhibitTimer**和**sr-TransMax**。通过BWP与小区的对应关系，可以确定一个SR配置在特定小区的特定上行BWP上使用的SR资源。

**在pucch-config中为各个上行bwp配置了sr资源**，**使用schedulingRequestResourceId来区分不同的资源配置**，schedulingRequestResourceId会关联schedulingRequestID，还包括SR发送时间点配置，以及对应的pucch resource配置。
![SchedulingRequestResourceId](http://1.116.145.136/NR/pucch_config.png)

下图是SR在接入过程的对逻辑信道的配置的示例，信令中对SRB1/SRB2/DRB都配置了响应的相同的SR资源。

![SchedulingRequestId](http://1.116.145.136/NR/逻辑信道SR_1.png)

##### [](#3-SR的发送)3. SR的发送

针对每个SR资源，基站通过**SchedulingRequestResourceConfig->resource**告诉UE使用PUCCH format0还是 format1来发送SR。
![SchedulingRequestConfig](http://1.116.145.136/NR/SchedulingRequestResourceConfig.png)
![SchedulingRequestId](http://1.116.145.136/NR/SchedulingRequestId_1.png)

在SchedulingRequestResourceConfig中的**periodicityAndOffSet**字段指定传送SR的PUCCH资源的周期,以符号或者slot为单位，确定了SR的发送时间位置[TS38.331 9.2.4]。可以看到SR的最小周期是2符号。
基于SR周期的差异，把SR对应三种场景来，如下图case所示：
![SchedulingRequestId](http://1.116.145.136/NR/SR周期发送1.png)

从协议指示的配置范围可以看出，根据不同的子载波间隔，可选择的周期偏移有所不同；当UE发现一个SR传输机会对应PUCCH资源所在的slot，用于改pucch传输的符号数小于对应pucch资源所需的符号数，UE不会在该slot上发送pucch资源；
SR资源是UE专用，UE发送SR时不需要指定自己的CRNTI，基站通过SR资源的时域、频域和码分复用信息就知道哪个UE请求上行资源。
![SchedulingRequestId](http://1.116.145.136/NR/SR发送机会.png)

协议描述中有**Positive SR**和**Negative SR**的概念，UE并不是一直有发送SR请求的需求，对于Positive SR即UE有SR请求发送，需要物理层发送**SR/PUCCH**，而对于无SR发送请求的UE，在SR资源的时间点，则该SR为Negative SR。基站不知道UE什么时候发送SR，需要在已经分配的SR资源上检测是否有SR上报，会在每个SR上来的时刻监测是否有SR资源，具体的SR调度流程如下图所示：
![SR调度流程](http://1.116.145.136/NR/SR流程_0.png)
SR调度流程注意事项：

- 只有在pucch资源上配置了SR才能发送SR请求上行调度，不然只能通过随机接入申请上行资源；

- SR发送实际在PUCCH的特定位置，以便基站能检测到SR；

- SR发送实际不能和测量GAP重合；

- 如果sr-ProhibitTimer未超时，则不能发送SR，此时定时器放置SR发送过多，降低PUCCH负载；

- 当UEUESR的传输实际和测量gap重叠或者sr-ProhibitTimer未超时等原因不能发送SR时，需要循环检测，指导条件满足；

- SR-COUNTER为计数器，只有当SR-COUNTER<sr-TransMax时，才能发送。为了放置SR重传发送过多，当SR成功触发UL GRANT后，SR-COUNTER会置零；

- UE触发SR后，这个SR级处于pending态，意思是为UE准备单是还没有发送SR，如果已经准备发送BSR，或者PUSCH资源足够，那么pending的SR会被取消。

UE发送SR之后，无法确定基站什么时候会下发UL Grant，如果UE等待超时（由果sr-ProhibitTimer定时器配置）就会尝试重发SR，每个SR都有自己的果sr-ProhibitTimer
**当SR请求发送达到最大次数仍然不能获得上行调度后，通过发送PRACH触发随机接入过程来获得上行调**；当SR的到达最大发送次数后无上行调度，除了发送PRACH外，MAC执行的操作还包括：

- 1.通知RRC Release所有服务小区PUCCH配置;

- 2.通知RRC Release所有服务小区SRS配置;

- 3.清除Configured DL assignments和 Configured UL Grants.

- 4.清除用于发送semi-persistent CSI的PUSCH Resource.

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
