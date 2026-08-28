---
title: 5G基础-NR接入过程
date: 2021-04-29 00:52:43
---

UE的接入无线承载网的过程，分为初始接入和随机接入两个过程；以下是NR SA的场景下的通过空口在UE和NW（网络）之间信令流程：
![image](/images/NR/Initial_Attach_Sequence.png)

##### [](#1-初始接入)1. [初始接入](#初始接入)

UE通过基站广播的PSS/SSS信息对齐频率、时间以及获得接入所需要的参数：

- 基站广播PSS/SSS/PBCH Block（SSB）：一个SSB有4个OFDM符号由PSS/SSS/PBCH组成，频域上有240个子载波（0-239编号）20个RB（一个RB12个子载波），协议38.211 7.4.2章节描述定义；

- UE检测到一个SS/PBCH Block,定位到PSS和SSS之后，UE便可以在时频域知道MIB消息的位置，应为MIB消息的位置是相对PSS和SSS固定的，**MIB消息的内容**有子载波间隔、**PBCH的DMRS符号位**置、**SIB1所在的时频位置**（由L3的PDCCH-Config SIB1配置）和**频带大**小、**是否禁止用户驻留**、是否容许用户直选到同频邻区等；解到MIB之后就可以解码PBCH的内容，获取SS/PBCH index、定时等信息；**SSB Block Pattern有五种case A-E**（1）SUB3G（A-C）其中FDD频段最大4个SSB Block，每个对应一个SSB Index；（2）sub3G-sub6G A-C定义8个SSB；（3）Above 6G （D-E）定义64个SSB；

> 
**38.321 6.1.4 MIB/SIB/OSI消息格式** 

> 
**SIB1消息**提供了UE准许接入和OSI调度的信息，以及初始BWP相关的频域位置和带宽，寻呼，PRACH接入等关键信息；SI-SchedingInfo 中SIB1字段中的valueTag字段在SI消息发生变化时都是加1，用于指示SI消息是否发生变化，UE使用这个字段检测自己之前保存的SI消息是否发生了变化；

- UE根据mib中中内容获取RSMI所在的频域位置（初始BWP的位置，PDCCH common coreset的视频位置），然后获取RMSI，并从RMSI中获取RACH的位置信息，上下行初始BWP的信息、PUCCH的配置等信息。

> 
BWP0阶段 PDCCH信道频域RB的位置与PDSCH信道是一致的

- UE在对应的RACH occasion上发送RACH Preamble。

##### [](#2-随机接入)2. [随机接入](#随机接入)

- **随机选择preamble Id发送preamble**：UE从SS_RSRP大于RSRP_ThreadSSB选一个SSB-RSRP最好的SSB波束。如果没有最好的额，就随机选一个SSB波束，在从SSB的preambles中随机选择一个preamble Id发送preamble，告诉基站有一个随机接入请求，并使基站估计与UE之间的传输时延，以便基站校准uplink timing并通过RAR的timing advance command告知UE；基站通过sib1广播告知UE，基站的RACH信道的视频位置、preamble序列格式、序列产生的参数等信息，用来解答UE在**什么时候**（prach-configuration-Index）以**在什么位置**（prach-frequencyOffset）以**什么格式**（prach-configuration-index）**多大的功率**（min(Pmax, PREAMBLE_RECEIVED_TAR_GET_POWER+Rl)）**发送RA**。

> 
**preamble的作用**：告诉基站有一个随机接入请求，并使基站估计其余UE之间的传输时延，以便在RAR中下发TAC；

- **基站下发RAR**：基站对不同的preamble分配不同（的TC-RNTI和UL-Grant资源等，并通过preamble测量到初始的TA值，在RAR中发送给UE；UE在发送Preamble之后，会在RAR的响应时间窗内，监听PDCCH以接收对应RA-RNTI的RAR；基站侧用RA-RNTI加扰的PDCCH的公共搜索空间中的的DCI1-0告诉UE。**如果UE在时间窗时间内发生**：
(1)**在时间窗内用RA-RNTI没有盲检到DCI**；
(2)**没有盲检到PDCCH CRC错误**；
(3)R**AR中的RA-PID与preambleId不一致**；
就认为本次**RA接入失败**；UE RA失败后重发preamble与RAR PDU中的BI(backoff Indicator)字段相关，如果RAR PDU中的携带了BI，那么UE会在0-BI中随机选取一个时刻重新发preamble接入；**BI的大小反映了小区的负载**；

> 
**RAR的响应时间窗**（RA Response Window）在rach-configGeneric中配置，配置项为ra-ResponseWindows单位是slot；

![RAR调度流程](http://1.116.145.136/NR/RAR调度流程.png)

- 
**UE发msg3 RRC_SETUP_REQ RRC承载建立请求**：UE收到RAR之后，通过解析，RAR的payload获取msg3发送时刻的视频资源，采用的mcs和功率等信息，UE在这个过程中会发送一个39bit的随机值（申请RRC Setup请求）；首次发送msg3时以RV版本号0发送（默认顺序0-2-3-1），若基站收到msg3 CRC错，则会基站通过以TC-RNTI加扰的DCI format0-0将信息告知UE；==**UE发送完成后msg3后会启动Ra-COntentResolution定时器**（这个定时器在sib1中的RACH-Config-Common中配置），在这个定时器期间UE会等待基站是否让自己接入，也就是冲突解决的过程；

- 
**基站发发msg4 RRC_CONN_SETUP，并进行冲突解决**: 在CCCH上发送，携带SIB1资源的配置的详细信息，同时如果有竞争接入会有竞争解决的过程，（冲突解决）基站把msg3的钱48bit作为UE Contention Resolution Identity MAC CE发送，其中包括了mcs的39bit随机值；msg4通过preamble Id和Tc-RNTI发送，终端收到ms4之后，会把竞争解决的MAC CE解析出来，和自己的msg3的前48bit对比，如果相等则认为自己竞争成功，否则竞争失败，在sib1的定时器之后再重新发起接入；

> 
**UE时RRC非连接态**：通过网络侧提供的S-IMSI作为标识，如果没有提，使用一个39bit的随机值作为临时标识，并通过CCCH告诉基站，基站在msg4时通过UE冲突是否解决；
**UE时RRC连接态**：msg3中UE通过C-RNTI MAC CE将自己的C-RNTI告诉基站，msg4中基站用这个C_RNTI来解决冲突。

- **UE发msg5 RRC_CONN_SETUP_COMPLETE，完成RRC链接建立**：通过DDCH的SRB1承载发送，携带上行方向的NAS消息，基站收到RRC_CONN_SETUP_COMPLETE消息之后，RRC链接建立完成，SRB2承载就会随之建立。

- **初始上下文建立**： 建立UE与AMF的之间的绘画，让UE与AMF之间保持交流，由AMF发起，初始上下文建立完成就表示UE与AMF之间完成连接；

- **UE能力查询**：UE能力主要有**UE的天线能力**（也就是支持的频段等信息）和**UE的网络能力**（加密算法，NAS等的支持情况）；基站根据核心网在UE上下文建立的请求中是否携带了UE能力，来决定是否进行查询，如果UE携带了则不下发UE能力查询。
**下图是NR SA UE注册的流程示例，从msg3一直到注册完成**；

![image](http://1.116.145.136/NR/SA_Initial_Registration.png)

**参考文献**：
[1]. [5G Standalone Access Registration Signaling Messages](https://www.eventhelix.com/5G/standalone-access-registration/5g-standalone-access-registration.pdf)
[2]. [5G Standalone Access: Registration Procedure](https://www.eventhelix.com/5G/standalone-access-registration/details/5g-standalone-access-registration.pdf)
[3]. [5G/NR-初始Aattach](http://www.sharetechnote.com/html/5G/5G_CallProcess_InitialAttach.html)

##### [](#3-随机接入触发的场景)3. [随机接入触发的场景](#随机接入触发的场景)

随机接入并不是只有新入网才会触发，还会发生在一些已经入网的UE身上，梳理出来主要有一下8大场景；

- 初始RRC连接建立：当UE需要和基站建立连接时，UE会发起随机接入；

- RRC重建：当UE需要和基站重新建立RRC连接时；

- UE进行切换时：UE会在目标小区发起随机接入；

- 下行数据到达：UE处于RRC连接，有UE有下行数据要发送，但发现UE失步，此时基站会通过下行控制信令让UE发起随机接入；若UE处在DRX休眠，则需要等UE变为Active后再下发控制信令；

- 上行数据达到：UE处于RRC连接，有UE有上行数据要发送，但发现UE失步，UE会发起随机接入；

- NSA接入，LTE小区接入，添加NR小区时，会发起随机接入；

- 基于RA请求SI：当UE需要请求特定的SI时，会发起随机接入；

- 当UE处在RRC-INACTIVE：（1）主动迁移到RRC-CONNECTED时，UE会发起随机接入；（2）基站需要UE迁移到RRC-CONNECTED是，基站会通过paging让UE发起随机接入；

> 
**其中**：**1、2、5是竞争接入**；3、4是优先非竞争接入，如果没有转悠preamble触发就是竞争的随机接入；6、7是非竞争接入；**8如果是UE发起的就是竞争接入，如果是网络侧发起的就是非竞争接入**。

##### [](#4-RRC重建与重同步)4. [RRC重建与重同步](#RRC重建与重同步)

###### [](#4-1-RRC重建)4.1 RRC重建

RRC重建是快速建立RRC连接的一种业务，重建的前提是UE已经与基站建立RRC连接，且已经成功启用安全模式，才能发起RRC重建；若是安全模式激活前发起重建，会发生重建失败，UE直接进入IDLE态，主要在**以下几个场景中会触发RRC重建**：

- 检测到无线链路链接失败（38.331 5.3.10），主要有**T310超时**、**RA失败且T311未运行**和**RLC达到最大重传**；

- NR-RRN系统内，异系统切换失败；

- L3收到底层完整性校验失败的报告；

- RRC连接重配失败；

###### [](#4-2-重同步)4.2 重同步

重同步的过程是UE重新接入的过程，是在UE或者基站有上行或者下行数据要发送时发现UE与基站失步了，由UE主动发起或者基站被动发起的同步动作；

- 对于SA基站收到msg4的ACK就认为上行同步；

- 对于NSA基站收到UE发送的首个数据报文（msg5）就认为上行同步；

**重同步的触发**：

- UE有上行数据要发，UE发现上行失步，有UE发起竞争重同步；

- 基站有下行数据要发，发现上行失步，有基站触发UE发起竞争重同步；

- SUL场景上下行数据到来，会触发重同步；

在基站和UE维护和管理同步状态是由一个**同步定时器**，

- 当UE收到基站的TA MAC CE时，会重启定时器；

- 基站收到UE的TA MAC CE时，也会重启定时器；

参考文献：
[1]. xxx大厂问题定位指导书

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
