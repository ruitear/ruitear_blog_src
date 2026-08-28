---
title: python工具包之matplotlib的使用--pyplot画图
date: 2018-07-15 01:01:56
tags:
  - python
  - matplotlib
  - plot
categories:
  - python
---

`Matplotlib` 是一个 `Python` 的 2D绘图库, 使用`matplotlib`能够非常简单的可视化数据。在`matplotlib`中使用最多的模块就是`pyplot`。`pyplot`非常接近`Matlab`的绘图实现，而且大多数的命令及其类似`Matlab`。在使用`Matplotlib`的时候需要import这个模块：
```
import matplotlib.pyplot as plot
```

  由于`matplotlib`默认字体没有中文，如果想让画图的标题支持中文的显示，必须手动添加中文字体方可，以下导入`SimHei`字体：

```
from pylab import mpl
mpl.rcParams['font.sans-serif'] = ['SimHei']
```

  画图的时候还会依赖数组和矩阵等科学计算，所以会对`numpy`产生依赖；所以还需要`import numpy`；下面将以线图、柱状图、饼图、极坐标图和散点图等几种典型的图表，介绍pyplot这个模块的画图功能。

##### [](#1-单个图表的线图)1. [单个图表的线图](#单个图表的线图])

  画单个线图和MATLAB，其实基本是一样的，首先选择一个X轴的范围，然后在这个范围内把[x,y]在对应的坐标区间画出来。完整的代码如下：
```
## 字体的默认设置没有中文，所以需要手动添加中文字体才能显示中文
from pylab import mpl
mpl.rcParams['font.sans-serif'] = ['SimHei']

import numpy as np
import matplotlib.pyplot as plot

##画单个图，多根线
def plotSignleImg():
    x=np.arange(0,5,0.2) # X轴的范围，0-5，点的间距是0.2个单位
    plot.plot(x,x**4,'r',x,x*90,'bs',x,x**3,'g^')  ## 'r'红 'bs'蓝 'g^'绿
    plot.show() # 显示画图结果
    
if __name__=='__main__':
    plotSignleImg()
```

  效果图：
![image](/images/pyplot/1.png)

##### [](#2-多个图表在一个图)2. [多个图表在一个图](#多个图表在一个图)

  在一个图表中画多个图，合画单个图的方式基本是一致的，唯一的区别就是，画多图的时候需要把画布分割成相应的块数，来放图表。如下例子，画两个图，需要命令`plot.subplot(211)`和`plot.subplot(212)`这两行代码中的数字前边两个必须一直，最后一个数代表图的位置，这里`1`表示上边的位置，`2`表示下边的例子。

```
## 画多个图，多根线
def plotMultiImg():
    x1=np.arange(0,5,0.05)
    x2=np.arange(0,5,0.05)

    plot.figure(1)  #启一个画布，用来承载图表的渲染，可忽略，默认会加载
    plot.subplot(211) #211 2：水平方向2个图，1：垂直方向1列，1：第1个位置
    plot.plot(x1,x1**4,'r',x1,x1*90,'bs',x1,x1**3,'g^',linewidth=2.0)

    plot.subplot(212) # 212 2：水平方向2个图，1：垂直方向1列，1：第2个位置
    plot.plot(x2,np.cos(2*np.pi*x2),'k')

    plot.show()
```

  效果图：
![image](/images/pyplot/2.png)

##### [](#3-画直方图-柱状图)3. [画直方图 (柱状图)](#画直方图 (柱状图))

  正态分布是大家接触最多的一种数据的呈现规则；这里以正态分布为例，介绍一下怎么画一个正态分布图的直方图；首先构造一组满足正态分布的数据，取分布在`[0,1]`随机的`1000`组数据，构造正态分布数据。`x=mu+sigma*np.random.randn(1000)`从标准正态分布中返回n=1000个样本值，这1000个数值的大小随机在[0,1]。

```
def plotHitImg():
    mu,sigma=100,15
    x=mu+sigma*np.random.randn(1000)  #从标准正态分布中返回n=1000个样本值。
    bins=100
    plot.hist(x,bins,normed=1,histtype='stepfilled',facecolor='b',alpha=0.75)

    n,bins,patches=plot.hist(x,100,normed=1,facecolor='k') 
    #k 表示黑色 b blue g green r red c cyan m magenta y yellow w white
    plot.xlabel("频率")
    plot.ylabel("概率")
    plot.title("直方图示例")
    plot.text(40,0.028,'均值=100 标准差=15')
    plot.axis([40,160,0,0.03])
    plot.grid()
    plot.show()
```

> 
**这里plot.hist()函数中**，

> 

> 
- bins将直方图的取值范围进行均等划分bings个区间；
> 
- normed =1 表示将出现频次进行了归一化。 normed=0，则为频次；
> 
- alpha表示直方图的透明度[0, 1] ；
> 
- histtype = ‘stepfilled’  表示去除条柱的黑色边框 , hist柱子类型
> 
- rwidth= :柱子与柱子之间的距离，默认是0
> 
- orientation:水平或垂直方向 

> 

> 
**plot.text()函数中**，

> 

> 
- text(x,y,string,fontsize=15,verticalalignment=”top”,horizontalalignment=”right”)
> 
- x,y:表示坐标值上的值 也就是字符串在图标的位置；
> 
- string:表示说明文字
> 
- fontsize:表示字体大小
> 
- verticalalignment:垂直对齐方式，参数:[center|top|bottom|baseline]
> 
- horizontalalignment:水平对齐方式，参数:[center|right|left]
> 

  效果图：
![image](/images/pyplot/3.png)

##### [](#4-画饼图)4. [画饼图](#画饼图)

  饼图，在分析数据时，主要呈现各份数据在总体的比例；相同颜色的数据标记组成一个数据系列。）显示为整个饼图的百分比。这里简单用水果的例子示意，总共有苹果、西瓜、猕猴桃和芒果等四种水果。它们分别占50%、12%、20%和18%。
```
def plotPieImg():
    labels=['苹果','西瓜','芒果','猕猴桃']
    size=[50,12,18,20]
    explode=(0,0.0,0,0)
    plot.pie(size,explode=explode,labels=labels,autopct='%1.1f%%',shadow=True,startangle=90,pctdistance = 0.6)

    plot.axis('equal')
    plot.legend()  #画出标签的图
    plot.show()
```

> 
**pyplot.pie()函数**：patches,l_text,p_text = atplotlib.pyplot.pie()

| 参数 | 默认值 | 描述 |
|---|---|---|
| x | NA | 数组。输入的数据用于创建一个饼图。 |
| explode | None | 数组，可选参数，维度为数据项的个数，如explode=(0,0.5,0,0) 第二个突出 |
| labels | None | 列表,可选参数，为每个饼块的标记。维度为数据项个数。 |
| colors | None | 数组,可选参数，用来标注每块饼图的颜色。None：将使用当前活动环的颜色。 |
| autopct | None | 字符串或函数，可选参数。如果不是None，是一个字符串或函数用带有数值饼图标注。 |
| pctdistance | 0.6 | 浮点数,可选参数,每个饼切片中心和通过autopct生成的文本开始之间的比例。如autopct是None,被忽略。 |
| shadow | False | 布尔值，可选参数，在饼图下面画一个阴影。 |
| labeldistance | 1.1 | 浮点数，可选参数，被画饼标记的直径。 |
| startangle | None | 浮点类型，可选参数，如果不是None，从x轴逆时针旋转饼图的开始角度。 |
| radius | None | 浮点类型，可选参数，饼图的半径，如果半径是None，将被设置成1。 |
| counterclock | rue | 布尔值，可选参数，指定指针方向，顺时针或者逆时针。 |
| wedgeprops | None | 字典类型,可选参数,参数字典传递给wedge对象用来画一个饼图.如：wedgeprops={‘linewidth’:3}线宽为3。 |
| textprops | None | 字典类型，可选参数，传递给text对象的字典参数。 |
| center | (0, 0) | 浮点类型的列表，可选参数，图标中心位置。 |
| frame | False | 布尔类型，可选参数，如果是true，绘制带有表的轴框架。 |
| rotatelabels | False | 布尔类型，可选参数，如果为True，旋转每个label到指定的角度。 |
| hold | None |  |
| data | None |  |

**返回值**：
patches：列表。matplotlib.patches.Wedge实例列表。
text：列表。matplotlib.text.Text实例label的列表。
autotexts：列表。A是数字标签的Text实例列表。  仅当参数autopct不为None时才返回。

  效果图：
![image](/images/pyplot/4.png)

##### [](#5-极坐标图)5. [极坐标图](#极坐标图)

  先简单说一下极坐标系：在平面内取一个定点O，叫极点，引一条射线Ox，叫做极轴，再选定一个长度单位和角度的正方向（通常取逆时针方向）。对于平面内任何一点M，用ρ表示线段OM的长度（有时也用r表示），θ表示从Ox到OM的角度，ρ叫做点M的极径，θ叫做点M的极角，有序数对`(ρ,θ)`就叫点M的极坐标，这样建立的坐标系叫做极坐标系。通常情况下，M的极径坐标单位为1（长度单位），极角坐标单位为rad（或°）。[参考 [极坐标](https://baike.baidu.com/item/%E6%9E%81%E5%9D%90%E6%A0%87/7607962?fr=aladdin)]。因此，画极坐标图，我们需要先建一个极坐标系，`ax=plot.subplot(111,projection='polar')`利用[subplot函数](https://matplotlib.org/devdocs/api/_as_gen/matplotlib.pyplot.subplot.html)在当前的figure中建立一个极坐标系，返回针对这个subplot的一个坐标轴Axes对象。然后设置这个`subplot`的[bar](matplotlib.axes.Axes.bar)，`bar(left, height, width=0.8, bottom=None, **kwargs)`设置一个矩形框，大小限定在`left`, `left+width`, `bottom`, `bottom+height`的范围，`left，bottom，height，width`四个参数决定位置和大小left决定了左边界，bottom决定了下边界，height决定了长度，width决定了宽度。对应到笛卡尔坐标系中，left决定了扇形的中线位置，然后height决定扇形的长度，bottom决定了下边界，width决定了扇形的宽度。

> 
**numpy**中**random.rand()**和**randn()** 

> 

> 
- random.randn(d0, d1, …, dn)是从标准正态分布中返回一个或多个样本值
> 
- numpy.random.rand(d0, d1, …, dn)的随机样本位于[0, 1)中
> 

```
#面向对象的极坐标图绘制
def plotPolorImg():
    N=20
    theta=np.linspace(0.0,2*np.pi,N,endpoint=False) #角度
    radii=10*np.random.rand(N) # 随机20个[0,10]的样本
    width=np.pi/4*np.random.rand(N) 随机20个[0,1]*2pi的样本，也就是

    ax=plot.subplot(111,projection='polar') #建立一个坐标系，projection='polar'表示极坐标
    bars=ax.bar(theta,radii,width,bottom=0.0)  #绘制一个条形图 #theta,radii,width分别是left，height， width
    for r,bar in zip(radii,bars):  #循环为每个bar对象设置颜色和透明度
        bar.set_facecolor(plot.cm.viridis(r/10.))
        bar.set_alpha(0.5) #添加颜色

    plot.show()
```

> 
**zip()函数** 将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表。
zip 方法在 [Python2](http://www.runoob.com/python/python-func-zip.html)和 [Python 3](http://www.runoob.com/python3/python3-func-zip.html) 中的不同：在 Python3 中为了减少内存，`zip()`返回的是一个对象。如需展示列表，需手动 list() 转换。
`bar.set_facecolor()` 窗口的背景颜色
`bar.set_edgecolor()` 窗口的边框颜色
`plot.cm.viridis()` 蓝-绿-黄colormap
`set_alpha()` 设置透明度，同plot的alpha参数，透明度[0, 1]
alpha表示直方图的透明度[0, 1] ；

  效果图：
![image](/images/pyplot/5.png)

##### [](#6-散点图)6. [散点图](#散点图)

  [散点图](#散点图)，顾名思义就是在图中分布的各个没有连接的孤点，也就是把某些点在坐标中明晰的表示出来。在数据的二维和三维表示中，用来发现数据分类和排布很有作用。
```
#散点图
def plotScatterImg():
    #返回值 fig : matplotlib.figure.Figure object   ax : Axes object or array of Axes objects.
    fig,ax=plot.subplots() 
    ax.plot(10*np.random.rand(10),10*np.random.rand(10),'o')
    ax.set_title('简单散点图') #设置图标标题
    plot.show()
```

  效果图：
![image](/images/pyplot/6.png)

##### [](#参考附表)[参考附表](#参考附表)

**python的颜色图**谱(colormap), 例如在`matplotlib.pyplot.imshow(X, cmap=None)`中

- X: 要绘制的图像或数组；

- cmap: 颜色图谱（colormap), 默认绘制为RGB(A)颜色空间。

| 颜色图谱 | 描述 | 颜色图谱 | 描述 |
|---|---|---|---|
| row 1 col 1 | row 1 col 2 | hsv | hsv颜色空间， 红-黄-绿-青-蓝-洋红-红 |
| row 2 col 1 | row 2 col 2 | inferno | 黑-红-黄 |
| autumn | 红-橙-黄 | jet | 蓝-青-黄-红 |
| bone | 黑-白，x线 | magma | 黑-红-白 |
| cool | 青-洋红 | pink | 黑-粉-白 |
| copper | 黑-铜 | plasma | 绿-红-黄 |
| flag | 红-白-蓝-黑 | prism | 红-黄-绿-蓝-紫-…-绿模式 |
| gray | 黑-白 | spring | 洋红-黄 |
| hot | 黑-红-黄-白 | summer | 绿-黄 |
| viridis | 蓝-绿-黄 | winter | 蓝-绿 |

##### [](#学习文献)[学习文献](#学习文献)

[1] 官方教程 : [https://matplotlib.org/users/pyplot_tutorial.html](https://matplotlib.org/users/pyplot_tutorial.html)
[2] matplotlib详细介绍 [https://blog.csdn.net/sunshine_in_moon/article/details/46573117](https://blog.csdn.net/sunshine_in_moon/article/details/46573117)
[3] matplotlib基础绘图函数示例 [https://www.jianshu.com/p/ad14fee5fb60](https://www.jianshu.com/p/ad14fee5fb60)
[4]matplotlib极坐标方法详解 [https://www.cnblogs.com/kallan/p/6738577.html](https://www.cnblogs.com/kallan/p/6738577.html)

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
