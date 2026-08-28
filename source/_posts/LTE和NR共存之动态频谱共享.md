---
title: LTE和NR共存之动态频谱共享
date: 2021-04-30 00:16:59
---

##### [](#1-背景)1. [背景](#背景)

5G频谱在全球各国进行的划分，以主力中低频段主力，中频段主要是TDD，低频段与原有的LTE频谱共用。图1-1是目前主要国家的频谱划分。

 ![image](http://1.116.145.136/NR/5G全球频谱.jpeg)图1-1 全球主要国家的5G频谱

频段的分配上，TDD是5G部署的主流，特别是亚太的中日韩地区；在5G部署的初期，TDD制式的主流的设备基本部署在以3.5G为主的频段上，没有历史的遗留问题要解决；但是并不是所有的国家或者运营商都有这样的频谱；在欧美地区，中频段的频谱已经被部分占用，比如北美3.5G频谱被军用卫星占用；除此之外，3.5G的覆盖也是相对差一些，如何提升覆盖，让用户5G的信号服务更广泛的用户，是当前运营商面对的主要问题之一。频谱的价格也比较昂贵，如何复用低频段的已有频谱频谱，快速部署5G，同样也是运营商面对的主要问题。
针对上述两个问题，3gpp提供了两种解决方案方法：（1）把5G部署到低频段里面与LTE共存；（2）让低频段辅助3.5G进行业务。基于运营商的困难和现实的瓶颈，各设备厂商提出了两个解决思路：（1）辅助上下行（SUL/SDL）;（2）LTE和NR共存。辅助上下行，不是这里讨论的内容；**当前只关注LTE和NR共存的问题**。

##### [](#2-LTE和NR的动态频谱共享)2. [LTE和NR的动态频谱共享](#LTE和NR的动态频谱共享)

动态频谱共享，从名字上看，在一块特定频谱上，LTE和NR在时域或者频域上同时存在，都有业务，资源复用。就是在时域和频域上划分，概括为FDM(频分)和TDM（时分）。如下图2-1所示。

 ![image](http://1.116.145.136/NR/FDM_TDM.jpeg)图2-1 TDM和FDM共享示意图

- **FDM**：(1)静态分割为独立载波，频带上无LTE使用的资源；(2)在保证LTE正常的业务的情况下，将LTE原本使用频域资源的频域资源进行划分，分给NR使用；

- **TDM**：(1)分时按子帧级别共享复用，在保证LTE正常业务的情况下，将某个子帧中除了LTE使用的控制信息使用的符号外，都分给NR使用。主要分为MBSFN和non-MBSFN两种情况。

具体到LTE和NR的业务实际，该如何实现共享呢？LTE的调度最小粒度是subframe，NR的最小粒度是slot。在subframe范围内，考虑LTE和NR的共享和共存，如图2-2的所示，主要是细分为四种场景：

- （1）在某个subframe NR使用LTE没用的频谱；

- （2）在某个subframe LTE都不使用，全部由NR使用频谱；

- （3）在LTE的MBSFN子帧中，LTE使用部分符号，剩下的由NR使用；

- （4）某个subframe LTE PDSCH不调度，NR使用个别符号调度。

![image](http://1.116.145.136/NR/LNR共存.png)

图2-2  LTE和NR共存是频谱共享的方式

##### [](#3-实现LTE和NR共存的方法)3. [实现LTE和NR共存的方法](#实现LTE和NR共存的方法)

根据运营商的实际需求和实现的系统复杂度，在3GPP R1‑1701618[1]会议的讨论如表3-1所述的5种方式；前三种Static FDM、Semi-static TDM和subframe级别的DSS(动态频谱共享)三种共享共存的方式是3gpp优先考虑的策略。第一个阶段是Static FDM，然后是Semi-static TDM，接着是subframe级别的DSS。目前这三个阶段宏站的设备厂商基本已经走完。

表3-1 动态频谱共享的实现方法

| 共享方式 | 描述 |
|---|---|
| Static FDM | 带宽为20 MHz，动态划分LNR使用比例，5 / 15、10 / 10或15/5 MHz分区等 |
| Semi-static TDM | 利用LTE DL MBSFN子帧和未使用的UL子帧的资源来调度NR |
| Subframe 级别DSS | 在时域上subframe级别使用LTE未使用的资源 |
| CLI级别DSS | 根据CLI mitigation of duplexing flexibility使用LTE未使用资源传输NR |
| PRB级别DSS | 在频域上在时域上子帧级别使用LTE未使用的资源 |

5G的制式和LTE一样，依然是TDD和FDD二分天下，由于TDD的频谱分配的先天优势，LTE和NR共存的场景和需求很少出现，而FDD因为LTE的历史频谱问题，有大量的存量网络，在网络从LTE到5G的升级换代的过程中，迫切的需要LTE和NR的频谱共享，制式共存。实现这种共享共存的技术，称为动态频谱共享，简称DSS。
![image](http://1.116.145.136/NR/共存频谱演示1.jpeg) ![image](http://1.116.145.136/NR/共存频谱演示2.jpeg) ![image](http://1.116.145.136/NR/共存频谱演示2.png)

图3-1  LTE和NR  FDM和TDM RE级别的频谱共享示意图

根据运营商的频谱和现网的测试情况，我们看到四大主设备厂商对LTE和NR的频谱共享各有侧重，在表3-2中简要列出。

###### [](#3-1-主流设备厂商的频谱共享方案)3.1 [主流设备厂商的频谱共享方案](#主流设备厂商的频谱共享方案)
表3-2  四大设备厂商的动态频谱共享场景

| 厂商 | 解决方案 | TDD/FDD制式 | 备注 |
|---|---|---|---|
| 华为 | Hybrid DSS | TDD和FDD | TDD主要考虑中移动 |
| 爱立信 | Ericsson SS | FDD |  |
| 爱立信 | Super DSS | TDD和FDD | TDD主要考虑中移动 |
| 诺基亚 | DSS | FDD |  |

从他们针对的运行商和测试结果来看，华为和中兴的TDD方案主要针对中移动N41获得的160M 2.6G频谱的解决大带宽部署LTE和NR共存的问题。诺基亚和爱立信在FDD上的耕耘，更富热情，大量存量的low band频段是FDD的LTE网络，在进入当前网络部署的阶段，如何快速的部署5G，与LTE共存，对他们的欧洲和北美客户来说是一个需要解决的现实问题。面对客户的痛点，他们通过动态频谱共享技术， 帮助客户进行提升网络部署，拓展业务。在FDD和TDD上，他们的方案有什么具体的差异，又是如何设计解决方案的呢？ 在接下的部分，将分别介绍四大设备厂商动态频谱共享在TDD和FDD的实现技术方案，其中TDD介绍介绍华为中兴的方案；FDD介绍爱立信和诺基亚的方案。

###### [](#3-2-动态频谱共享IN-TDD)3.2 [动态频谱共享IN TDD ](#动态频谱共享IN_TDD)

动态频谱共享在TDD的实现，华为和中心的方案基本差不多，在支持mimo的AAU上保证4G不移频的情况下，主要部署2.6G NR。如图3-2所示，是中兴的频谱共享方案和2.6G的演进变化。主要是预期三个阶段：

![image](http://1.116.145.136/NR/dss中兴.jpeg)

图3-2 中兴在中移动2.6G上的动态频谱共享解决方案

- 阶段1：在不使用LTE和NR动态频谱共享之前的，LTE大面积在网部署的时候，在2515-2575范围内，固定部署60M的NR，剩下的100M都部署LTE的载波。

- 阶段2：当5G进入中期部署，NR已经规模部署，还存量大量的LTE网络，在2575-2595范围的40M，根据LTE和NR的负载情况，动态部署NR和LTE的载波，进行动态频谱共享，在40M的载波中动态的建0或1或2个20M的载波。

- 阶段3：当NR进入连片组网，LTE网络开始进入中后期，存量下降，将频谱共享的频段完全固定作为NR带宽，保证满带宽100M的，进行整体连片100M NR的建网。

华为的方案是什么样的呢？如图3-3所示，根据LTE和NR的负载情况，动态的决定频谱属于哪种制式使用，这种迁移相当于静态的频谱划分，属于静态FDM的范畴。也是2个20M的带宽，为什么是20M呢？因为LTE最大支持的载波就是20M。跟中兴的方案，总体上差别不大。

![image](http://1.116.145.136/NR/dss华为.jpeg)

图3-3 华为在中移动2.6G上的动态频谱共享解决方案

###### [](#3-3-动态频谱共享IN-FDD)3.3 [动态频谱共享IN FDD](#动态频谱共享IN_FDD)

在FDD的网络中，爱立信和诺基亚存量的份额比较高，相对华为和中兴，有市场的优势。在存量LTE和新部署NR的融合过程中，如何利用好低频的FDD网络快速部署NR，爱立信和诺基亚更积极一些。这里主要讲一下他们的动态频谱共享方案。图3-4是他们的频谱共享演进示意图。这里将主要介绍在FDD诺基亚和爱立信实现的协议细节，具体的解决方案将在下个章节进行介绍。

![image](http://1.116.145.136/NR/dss_fdd.png)

图3-4  LTE和NR动态频谱共享的 IN FDD 示意图

FDD上下行频段分开，不存在上下行串扰，很容易利用上行或者下行的载波资源，用来补充NR的业务。例如SUL，利用FDD的上行频段扩展NR的覆盖。在频谱共享的中，如何利用到FDD的上下行资源呢？上行频段基站的行为较少，比较容易控制；下行频段基站的测量信号和信号比较多，在处理时就需要考虑最大化利用LTE或者NR频谱，让LTE和NR高效的融合，例如： SSB，CSIRS，TRS，DMRS，PDCCH等等。接下来将详阐述这些资源在FDD制式中，如何以LTE共享共存。

![image](http://1.116.145.136/NR/DSS_NON_MBSFN.png)

图3-5 LTE和NR共存是NR SSB的放置示意图 in Non-MBSFN

###### [](#3-3-1-如何放置SSB)3.3.1 [如何放置SSB](#如何放置SSB)

NR PDSCH传输的情况下，SSB资源分配与LTE CRS冲突，而且由于帧结构的差异，子载波间隔也可能不同。LTE有MBSFN的子帧，在MBSFN子帧中，除了前两个符号被用于传输控制信号外，其他的符号都可以空出来。那么，在处理NR数据时，就要考虑MBSFN和非BMBSFN。

> Non-MBSFN子帧

NR的SSB需要4个连续的符号，不能更改LTE或者NR的SSB结构，唯一的方法就是在找到资源让NR的SSB挤到进去。如图3-5所示SSB在不同case下的可能放置的情况，LTE的CRS使用的RE与天线port数相关，下图示例为1/2/4port场景。
LTE的子载波间隔固定在15KHz，对应NR的3种case。

- Case 1： NR和LTE都是15kHz，NR连续4个符号的SSB都会与CRS冲突；

- Case 2：NR 30kHz，占用2符号的LTE是可行的；

- Case 3：NR 30kHz，在1/2port的LTE是能够放置的，在4port时会与LTE的CRS冲突。
当LTE和NR的子载波间隔不同的时候15kHz和30kHz，会破坏正交性，不能按常规的并排放置数据，需要加保护间隔，如图3-6所示。保护带宽虽然能避免喜好的冲突，但是无法兼顾所有的case场景，而且还会让带宽利用率减低。因此在Non-MBSFN的不太适合放SSB信号。
PALCE_SSB9.png

![image](http://1.116.145.136/NR/PALCE_SSB9.png)

图3-6  LTE15kHz和NR 30kHz SSB共存放置的示意图

> 
MBSFN子帧
怎么才能兼顾到多场景的case，还不用考虑LTE由于port数配置的差异导致的？MBSFN子帧，恰到好处！MBSFN子帧不能完全为空。需要定义了一个非MBSFN区域，该区域的长度可以是1-2个OFDM符号。该区域旨在承载用于LTE的控制信道，如PHICH，PCFICH和PDCCH等。NR传输只能在MBSFN子帧内从OFDM符号2或3处开始。在MBSFN子帧中不用考虑CRS的影响。图3-7示意在MBSFN中如何放置NR的SSB，在case 1/2/3的时都可以完美的放下至少1个SSB。

![image](http://1.116.145.136/NR/DSS_IN_MBSFN.png)

图3-7 LTE和NR共存是NR SSB的放置示意图 in MBSFN

LTE中有6个帧可以配置为MBSFN子帧，分别是#1,#2, #3, #6, #7, #8. 为了减少MBSFN对LTE的性能影响，通常只配置1个MBSFN子帧，用sib2进行广播。bypass掉MBSFN subframe中LTE符号的情况下，剩余的符号可以传输 NR SSB, SIBs, CSI-RS, TRS and PDSCH data 等。

![image](http://1.116.145.136/NR/SSB_TYPE.png)

从对LTE吞吐量的影响来看，基于MBSFN频谱共享，通常不是数据的最佳选择。但是，使用MBSFN子帧对于基于SSB SCS提供SSB传输非常重要，因此是首选的DSS解决方案。

【注意】这里case3 虽然时针对TDD得场景，但是考虑到上下信号得干扰和资源使用率以及客户得需求，TDD下得TDM方式得动态频谱方案暂时没有厂商实现。

###### [](#3-3-2-如何放PDCCH和PDSCH)3.3.2 [如何放PDCCH和PDSCH](#如何放PDCCH和PDSCH)

与放置SSB信号类似，如何放置PDCCH，也需要考虑MBSFN和Non-MBSFN的情况。在Non-MBSFN的子帧中，频谱上直接同时铺LTE和NR数据，**如3-8图所示**，NR CORESET与LTE控制信道位置冲突。如果将coreset往后挪来给2个符号只放到符号2上，但是仍然会有NR PDSCH与LTE CRS重叠，与LTE中的控制信道类似，无论PDSCH区域的大小如何，CRS对整个频带上的所有设备都是可见的。无法正常工作。

![image](http://1.116.145.136/NR/pdsch_pdcch_gong_slot.png)

图3-8 NRPDCCH和PDSCH和LTE同 slot共铺数据示意图（1）

如何保证LTE和NR的数据都能在频谱上不冲突的时候的共享编码呢？除了CRS还会有其他的信号干扰到NR的PDSCH嘛？还有SSB。针对SSB和CRS，可以采用，ratematching的方式（图3-10是CRS RateMatching示意图），，将他们所在的RB或者RE打孔掉，不放置NR的PDSCH数据，同时把NR的CORESET / PDCCH配置到位于符号2。这样就能解决PDCCH和PDSCH传输的问题。

![image](http://1.116.145.136/NR/pdcch_pdsch_gong_slot_2.jpeg)

图3-9 NRPDCCH和PDSCH和LTE同 slot共铺数据示意图（2）

![image](http://1.116.145.136/NR/wu_crs_ratematching_1.png)

图3-10 无LTE-SSB的时候crs ratematching，单符号DMRS，无附加导频

![image](http://1.116.145.136/NR/crs打孔示意.png)

图3-11  LTE-SSB和LTE-CRS rate matching的打孔示意图

当PDCCH符号放到了符号2，那么PDSCH的符号必须从符号3开始，由于pdsch dmrs的占用，pdsch在不使用dmrs剩余符号的时候只能从符号4开始，因此无法在pdsch typeA的时候进行频谱共享。需要将pdsch的type类型调整为typeB。图3-12 展示的tapeA和typeB的差异。

![image](http://1.116.145.136/NR/nr_typea_typeb_差异.png)

图3-12 NR PDSCH typeA和typeB的差异 示意图

###### [](#3-3-3-如何放置PDSCH-DMRS)3.3.3 [如何放置PDSCH DMRS](#如何放置PDSCH_DMRS)

NR pdsch的DMRS导频信号，根据配置的天线数和映射方式有一定的差异，在LTE的 FDD一般都是4port以下，因此这种影响基本可以忽略掉，稍微简单处理，考虑单符号的导频即可，最多3个附加导频信号场景即可。
图3-13是NR PDSCH DMRS单符号，3个附加导频的情况；第3个附加导频在LTE为1/2/4port的时候都会与CRS产生冲突。如何规避掉部分呢？跟PDSCH一样打孔掉嘛？可能行不通，因为，LTE的CRS随着小区的PCI在频域的符号是有变化，而且NR的DMRS也会随着天线端口变化，考虑从RE级规避是不现实的。
![image](http://1.116.145.136/NR/nr_place_dmrs.png)

图3-13  NR PDSCH DMRS放置示意图（1）

将最后一个DMRS的符号位置移动一下，可行嘛？如图3-14，把NR的第三个附加导频符号往后移动一个，可以完美的避开LTE的CRS，不论是1/2/4port的哪种场景都OK。

![image](http://1.116.145.136/NR/nr_place_dmrs_1.png)

图3-14  NR PDSCH DMRS放置示意图（2）

###### [](#3-3-4-上行链路的调整)3.3.4 [上行链路的调整](#上行链路的调整)

在NR和LTE中均使用15 kHz SCS的时候， NR载波不会与LTE载波完全映射在同一频率网格上。 NR和LTE上行子载波映射之间的差异约为7.5 kHz，如图3-16所示。需要加以缓解，否则会由于 LTE和NR的非正交子载波而引起载波间干扰。为了应对这种情况，在NR需要引入了7.5 kHz频移，这是所有进行动态频谱共享部署的必需功能。此处的偏移通过sib消息通知UE，配置项如图3-15所示。

![image](http://1.116.145.136/NR/上行频偏.png)

图3-15  NR上行频偏的配置项

![image](http://1.116.145.136/NR/上行移频示意图.png)

图3-16  NR PDSCH 上行移频示意图

###### [](#3-3-5-需要的UE能力)3.3.5 [需要的UE能力](#需要的UE能力)

为了在LTE和NR频谱的共享共存的过程中，让业务保持下去，在不改变LTE网络的前提下，需要NR UE的支持一下几个功能：

- 支持PDCCH放置在符号2

- 能进行LTE-SSB的rate-matching

- 支持TRS放置在符号6和10

- 支持灵活的CSIRS放置

- 当配置LTE CRS的时候，支持附加导频符号后移

- 上行7.5kHz的偏移

- 当配置LTE CRS的时候，支持LTE CRS的ratematching

- 支持NR pdsch typeB的映射方式

表4-1 中国区三大运行商的频谱和DSS的需求情况

| 电信运营商 | 5G频谱 | 存量频谱 | 动态频谱共享的需求 |
|---|---|---|---|
| 中国移动 | N41 2.6G 160M/N79 4.9G 100M | 2.6G TD-LTE | 2.6G TD网络的主力频段，中移动独享超大带宽160M，DSS需要适配大带宽网络 |
| 中国电信 | N78 3.5G 100M | 2.1G  FDD-LTE | 3.5G不需要DSS，2.1G需要 |
| 中国联通 | N78 3.5G 100M | 2.1G/1.8G FDD-LTE | 3.5G不需要DSS，2.1G需要 |

##### [](#4-如何设计自己的动态频谱共享方案？)4. [如何设计自己的动态频谱共享方案？](#如何设计自己的动态频谱共享方案)

5G在发展和部署的阶段，逐渐模糊了宏站和小站的区别。在客户来讲，他们似乎并不觉得小站要比宏站差多少，除了规格的和尺寸之外。从各大主设备厂商的主力产品和他们面向的的大众市场来看，他们基于需求和场景的差异，对动态频谱共享各自的侧重点。参考宏站厂商的策略， BaiCells该如何布局动态频谱共享。

###### [](#4-1-华为和中兴TDD动态频谱共享的需求背景)4.1 [华为和中兴TDD动态频谱共享的需求背景](#华为和中兴TDD动态频谱共享的需求背景)

中兴和华为面对的大本营市场是TDD的NR网络，中移动是市场的关键。中移动的2.6G频谱中有存量的LTE网络；由于带宽比较大，让LTE和NR共存的方式进行动态频谱共享，并不适应大带宽的部署；而且，他们的主设备是MIMO 8T8R的TDD以上的设备，不适合进行符号级的共享，因为LTE大多数并没有走进8流以上的MIMO；除此之外，处理多MIMO的符号级共享实现的复杂的更高，带宽利用率也不理想，在上下行共slot的时候会浪费更多。基于这些考虑，针对中移动的载波级共享的，华为和中兴提出了动态频谱共享的Hybrid DSS和 Super DSS，在3.2节中已经介绍。针对电联的FDD的网络，华为和中兴的实现与爱立信和诺基亚并本质的区别，场景更突出，这里就不讨论。

![image](http://1.116.145.136/NR/TDD_160dss.png)

图4-1  TDD 100M 40M在动态频谱共享示意图

###### [](#4-2-爱立信和诺基亚在FDD动态频谱共享的需求背景)4.2 [爱立信和诺基亚在FDD动态频谱共享的需求背景](#爱立信和诺基亚在FDD动态频谱共享的需求背景)

在c-band的大带宽频谱范围内，没有考虑频谱共享，因为这些频谱中不存在LTE网络。在欧美，频谱价格比较昂贵，每个运行商拿到的频谱范围都有差异，面对昂贵的频谱和中高频覆盖的劣势，每个运营商拿到频谱有限； 这些优势带宽，在北美被军事用途占据不发提供运营； 只有穿透力更弱的FR2频谱， 面对这样的现实问题，低频段广覆盖的优势，在5G部署的时候显得格外珍贵。在这样的背景下，爱立信和诺基亚分别提出了自己的基于动态频谱共享的方案。

表4-2 欧美主要地区频谱现状

| 市场区域 | 5G频谱 | 主要存量频谱 | 动态频谱共享的需求 |
|---|---|---|---|
| 北美 | C-band 被军用卫星占用授权FR2的频谱 | Low band FDD | 高频不需要频谱共享，频谱太贵，低频需要重耕使用 |
| 欧洲 | C-band和FR2的频谱 | Low band FDD | 高频不需要频谱共享，频谱太贵，低频需要重耕使用 |

诺基亚的方案与爱立信的本质上并无太大的差别，在低频段FDD网络部署动态频谱共享，通过低频与中频的CA扩展中频的覆盖。在处理的时间间隔上，诺基亚通过对比不同时间切换网络收益情况，采用了10ms的周期切换间隔。认为10ms在回报比和开销上是最好的。诺基亚还提到了网络切片技术是DSS的发展方向。

![image](http://1.116.145.136/NR/诺基亚DSS.jpeg)

图4-2 诺基亚动态频谱共享解决方案

图4-3为爱立信的频谱共享解决方案，通过中频+低频网络的结合，成倍的扩展5G的覆盖；同时也解决了快速部署5G网络的烦恼。在部署中，通过上行行开关控制频谱共享的链路方向，在UE支持同时中频和低频的band时，利用载波聚合，结合中频和低频的频谱，这样就可以让远、中、近的用户都感受到NR网络便利。爱立信实现了1ms的高效快速地动态频谱共享。相应的技术细节在3.3节已经介绍。

![image](http://1.116.145.136/NR/爱立信dss1.jpeg) ![image](http://1.116.145.136/NR/爱立信dss2.jpeg)

图4-3 爱立信的动态频谱共享部署解决方案

查找动态频谱共享在TDD的实现的相关内容，看到了罗德公司关于DSS在TDD的描述[2]。从问答的描述可以看出，实现DSS的关键工具MBSFN主要是为FDD设计的，TDD的网络基本不需要与LTE共享（除了中移动160M的频谱），因为5G的TDD网络在欧美，不是没事用就是对LTE不可用，因此对DSS的需求也几乎没有。

![image](http://1.116.145.136/NR/罗德dss.png)

图4-4 关于DSS的部分问答

针对不同客户的不同场景，你的解决方案是什么呢？

##### [](#参考资料)[参考资料](#参考资料)

[1]. [33060-dynamic-spectrum-sharing-is-a-game-changer](https://www.microwavejournal.com/blogs/9-pat-hindle-mwj-editor/post/33060-dynamic-spectrum-sharing-is-a-game-changer)
[2]. [DSS_QA.pdf](https://cdn.rohde-schwarz.com/ymkt/na/content/DSS_QA.pdf)
[3]. [33239-nr-and-lte-coexistence-through-dynamic-spectrum-sharing](https://www.microwavejournal.com/articles/print/33239-nr-and-lte-coexistence-through-dynamic-spectrum-sharing)
[4]. [5G_DSS.html#Ref_05](https://www.sharetechnote.com/html/5G/5G_DSS.html#Ref_05)
[5]. [Dynamic-Spectrum-Sharing-WhitePaper-PDFDSSWP-031320.pdf](https://newsletter.mediatek.com/hubfs/mediatek5gprogress/Dynamic-Spectrum-Sharing-WhitePaper-PDFDSSWP-031320.pdf)
[6]. (A4) 5G NR Dynamic Spectrum Sharing Drivers & Test Implications_Alex Liang.pdf
[7]. [implementing-dynamic-spectrum-sharing](https://www.ednasia.com/implementing-dynamic-spectrum-sharing/)
[8]. [article-dss-enabling-5g-nr-in-standard-lte-subframes-part-1-_254008.html#media-gallery-7](https://www.rohde-schwarz.com/us/solutions/test-and-measurement/mobile-network-testing/stories-insights/article-dss-enabling-5g-nr-in-standard-lte-subframes-part-1-_254008.html#media-gallery-7)
[9]. [sarticle-dss-5g-nr-lte-coexistence-through-dynamic-spectrum-sharing-part-1-_254002.html#media-gallery-8](https://www.rohde-schwarz.com/us/solutions/test-and-measurement/mobile-network-testing/stories-insights/article-dss-5g-nr-lte-coexistence-through-dynamic-spectrum-sharing-part-1-_254002.html#media-gallery-8)
[10]. [implementing-dynamic-spectrum-sharing](https://www.ednasia.com/implementing-dynamic-spectrum-sharing/)
[11]. [nr-lte-coexistence-dynamic-spectrum-sharing-dss](https://www.mwee.com/design-center/nr-lte-coexistence-dynamic-spectrum-sharing-dss)
[12]. [RWS-180011.pdf](https://www.3g4g.co.uk/5G/5Gtech_4001_3GPP_5GNR_IMT2020_EvaluationWorkshop_Oct2018/RWS-180011.pdf)
[12]. [dynamic-spectrum-sharing-for-5g-nr-and-4g-lte-coexistence](https://www.rcrwireless.com/20201117/opinion/dynamic-spectrum-sharing-for-5g-nr-and-4g-lte-coexistence)
[13]. [new-3gpp-effort-on-nr-in-unlicensed-spectrum-expands-5g-to-new-areas.pdf](https://www.qualcomm.com/media/documents/files/new-3gpp-effort-on-nr-in-unlicensed-spectrum-expands-5g-to-new-areas.pdf)
[14]. [2020-08-26_ITU_Spectrum-Planning-for-Emerging-Technologies_Ericsson.pdf](https://www.itu.int/en/ITU-D/Conferences/GSR/2020/Documents/2020-08-26_ITU_Spectrum-Planning-for-Emerging-Technologies_Ericsson.pdf)
[15]. [1805.05591.pdf](https://arxiv.org/pdf/1805.05591.pdf)
[16]. [public_policy_position_5g_spectrum.pdf](http://www-file.huawei.com/-/media/CORPORATE/PDF/public-policy/public_policy_position_5g_spectrum.pdf)
[17]. [dss-the-5g-deployment-x-facto](https://blog.viavisolutions.com/2020/08/12/dss-the-5g-deployment-x-factor/)
[18]. [10109-Industry-s-First-Tri-RAT-Dynamic-Spectrum-Sharing-Solution-for-Realizing-Fast-5G-Deployments](https://www.everythingrf.com/News/details/10109-Industry-s-First-Tri-RAT-Dynamic-Spectrum-Sharing-Solution-for-Realizing-Fast-5G-Deployments?utm_source=latest)
[19]. [33867-zte-and-china-unicom-implement-industrys-first-tri-rat-dynamic-spectrum-sharing-solution](https://www.microwavejournal.com/articles/33867-zte-and-china-unicom-implement-industrys-first-tri-rat-dynamic-spectrum-sharing-solution)
[20]. [dynamic-spectrum-sharing-standardization](https://www.ericsson.com/en/blog/2019/6/dynamic-spectrum-sharing-standardization)
[21]. [first-cloudair-lte-tdd-nr-dynamic-spectrum-sharing-commercial-verified-by-china-mobile-and-huawei](https://www.mobileworldlive.com/huawei-updates/first-cloudair-lte-tdd-nr-dynamic-spectrum-sharing-commercial-verified-by-china-mobile-and-huawei/)

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
