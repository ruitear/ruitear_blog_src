---
title: PDSCH的MCS选择与码率确定以及TBsize的计算和RB预估
date: 2021-05-21 00:00:13
---

##### [](#1-mcs的选择和码率的确定)1. [mcs的选择和码率的确定](#mcs的选择和码率的确定)

对月PDSCH的调度，主要有两种方式的**一种是**有PDCCH并通过C-RNTI, MCS-C- RNTI, TC-RNTI, CS-RNTI, SI-RNTI, RA-RNTI, 或者 P-RNTI加扰，下发DCI_1_0或者DCI_1_1来调度；**另一种是**在没有PDCCH的情况下通过高层提供的SPS-Config配置来进行PDSCH调度。
PDSCH调制阶数通过DCI中5bit的字段`I_MCS`来指数，5bit最大32，协议中定义了3张mcs表，分别是1个256QAM，2个64QAM的表；在不同的场景选用不同的mcs表。比如UE不希望在解码P-RNTI, RA-RNTI, SI-RNTI加扰的PDSCH时，使用Qm > 2的调制方式。

**case1**： 高层配置PDSCH-config种使用**qam256**的mcs表，那么对DCI1_1指示的PDSCH使用**Table 5.1.3.1-2**来确定MCS和码率，也就是256qam的表格。

**case2**： 如果UE没有通过SPS-config来配置mcs表，而是通过pdsch-config配置了成**qam256**，那么UE使用**Table 5.1.3.1-2**来确定MCS和码率，也就是256qam的表格。
![pdsch_mcsTable2](/images/NR/pdsch_mcsTable2.png)

**case3**： 如果UE没有配置MCS-C-RNTI，且高曾参数pdsch-config种配置的mcs表是**qam64LowSE**，那么针对UE USS 搜索空间的对应的PDSCH调度使用**Table 5.1.3.1-3**来确定MCS和码率，也就是低码率的64qam的表格。

**case4**： 如果UE配置了MCS-C-RNTI，pdsch是被MCS-C-RNTI加扰的PDCCH指示调度，那么UE使用**Table 5.1.3.1-3**来确定MCS和码率；

**case5**： 如果UE的mcs表通过SPS-Config配置并且配置成**qam64LowSE**，那么UE使用**Table 5.1.3.1-3**来确定MCS和码率；
![pdsch_mcsTable3](/images/NR/pdsch_mcsTable3.png)

**case6**：除了上述5种情况外，使用**Table 5.1.3.1-1**；
![pdsch_mcsTable1](/images/NR/pdsch_mcsTable1.png)从上述的mcs表种可以看出，当mcs确定之后相应的传输码率就确定了。因此码率是通过mcs查表间接确认得到的。

##### [](#2-TBsize的计算和RB预估)2. [TBsize的计算和RB预估](TBsize的计算和RB预估)

pdsch传输的tbsize宏观上见与码字数和码率强相关，码字由基站高层参数`maxNrofCodeWordsScheduledByDCI`指示，表示使用1个码字还是两个码字；码率由上mcs表确定，针对64qam可以使用mcs 0-28；256qam可以使用0-27；

###### [](#2-1-TBsize的计算)2.1 [TBsize的计算](TBsize的计算)

一个slot对应的PDSCH可用的TBSIZE计算公式如下，在PDSCH可用的RE是上除去DMRS占用和PDCCH占用的RE等资源占用以后的频谱资源。一个slot种pdsch的有效RE数由如下公式获得：

其中：`N ^{RB}_{sc}`是一个RB的子载波个数，`N^{sh}_{symb}`是时域上一个slot对应的符号数,`N^{PRB}_{DMRS}`是一个RB上DMRS占用的RE数；`N^{PRB}_{oh}`在高层参数PDSCH-ServingCellConfig->xOverhead配置，可配置为0，6，12，18；当xOverhead没有配置或者调度pdsch的pdcch由SI-RNTI, RA-RNTI 或者 P-RNTI加扰时，`N^{PRB}_{oh}`是0。
一个slot内频域上所有的pdsch可用RE，由以下公式获取：
$$
\begin{equation}
N_{RE} = min(156, N^{‘}*{RE})*n*{prb}
\end{eqnarray}
$$
其中，`n_{prb}`是slot内可分配的PRB数。

有了RE数解可以计算当前的TB-Size了，TB-Size的计算如下：

$$
\begin{equation}
N_{info} = N_{RE}*R*Q_{m}*v
\end{eqnarray}
$$
其中，R是目标码率，Qm是调制阶数，V标识layer数。

有了`N_info`之后还需要跟3824作量化比较才能确认最终的tbsize：

- `N_info` <= 3824 : 在N_info的小于3824的场景下确定TB-Size需要先量化，然后再查表确认TB-Size;量化公式如下：

$$
\begin{equation}
N^{‘}*{info} = max(24, 2^{n}*⌊\frac{N*{info}}{2^n} ⌋)，其中 n = max(3, ⌊ log_2(N_{info})⌋-6)
\end{eqnarray}
$$
通过量化公式获得的`N_'_info`在Table 5.1.3.2-1中查找不小于他的最接近的值，比如算出来的量化值是606，那么通过查表就获得的TB-Size就是608；
![tbs_low_3824](/images/NR/tbs_low_3824.png)

- `N_info` > 3824 :

$$
\begin{equation}
N^{‘}*{info} = max(3840, 2^{n}*round(\frac{N*{info}-24}{2^n} ))，其中 n = ⌊ log_2(N_{info}-24)⌋-5)
\end{eqnarray}
$$

**当码率R<=1/4时**，
$$
\begin{equation}
TBS = 8*C* ⌈(\frac{N^{‘}*{info}+24}{8*C})⌉-24,其中C=⌈\frac{N^{‘}*{info}+24}{8424}⌉
\end{eqnarray}
$$

**当码率R>1/4时**，
**(1)** 如果`N_'_info`>8424, 那么TBS通过以下公式计算：
$$
\begin{equation}
TBS = 8*C* (\frac{N^{‘}*{info}+24}{8*C})⌉-24,其中C=⌈\frac{N^{‘}*{info}+24}{3816}⌉
\end{eqnarray}
$$
**(2)** 如果`N_'_info`<=8424, 那么TBS通过以下公式计算：
$$
\begin{equation}
TBS = 8*C* (\frac{N^{‘}_{info}+24}{8})-24
\end{eqnarray}
$$
需要注意的是：**(1)** 当调度PDSCH的PDCCH是通过SI-RNTI加扰的话，也就是传输sib1消息时，TB-Size的大小不能超过2976，因为sib1只能通过qpsk的调制方式发送；**(2)** 当调度的DCI是P-RNTI、RA-RNTI加扰的时候，在信息量N_info计算时需要带上一个缩放因子，具体如下图所示：
![tbs_Scaling_factor](/images/NR/tbs_Scaling_factor.png)
那么新的`N_info`计算公式就编程如下的样子：
$$
\begin{equation}
N_{info} = S*N_{RE}*R*Q_{m}*v, 其中S是缩放因子
$$

###### [](#2-2-RB预估)2.2 [RB预估](RB预估)

UE进行上下行数传的时候，基站根据UE的数据量来判断UE的调度时刻使用的RB数，数据量的计算公式如下：
`$$
\begin{equation}
数据量 = {N_{prb}*N_{re}*layer*Qm*CodeRate},
$$
N_prb是传递这些数据量需要的RB数，N_re是每个RB中有效的RE数，layer是数传的UE的rank，QM是调试bit数，CodeRate是传输的码率。

预估RB就是通过上边的公式反算UE传输当前数据量需要的RB数，在确定RB数之前需要先确定，每个RB中的RE数，这些RE数跟基站的配置相关，特别是PDCCH和PDSCH DMRS的配置。

> 
PDSCH DMRS : NR中定义了两种DMRS type，分别是type1和type2，在这两种type中，有1符号或者2符号的DMRS，除此之外还可以配置额外的附加导频的DMRS。

![pdsch_pusch_type](/images/NR/pdsch_pusch_type.png)

- 
PDSCH DMRS Type A ：固定在第三或(pos2)者第四(pos3)个符号上，无论PDSCH的起始符号是多少，最小资源组是一个RE；

- 
PDSCH DMRS Type B ：DMRS固定在分配PDSCH的第一个符号上；最小资源组是2个RE；

- 附加导频   ，可以配置0-4个

【参考文献】：[https://www.sharetechnote.com/html/5G/5G_PDSCH_DMRS.html](https://www.sharetechnote.com/html/5G/5G_PDSCH_DMRS.html)

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
