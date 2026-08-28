---
title: windows下基于python的Tensorflow环境搭建
date: 2020-05-17 20:52:10
tags:
  - 环境搭建
categories:
  - Python
---

windows下搭建python的tensorflow环境，最简单地是利用pip安装。

##### [](#1-环境准备)1. [环境准备](#环境准备)

下载`python`，`setuptools`，`pip`. 安装python后在，将python.exe所在的路径添加到环境变量中，
```
D:\Python\Python37  假设python.exe所在的路径
```

从[pypi源](https://pypi.org/project/setuptools/#files)上下载`setuptools`，下载压缩包版本的，解压之后，将setuptools拷贝到python安装路径下的tool路径下即可。
```
D:\Python\Python37\Tools  假设python的tools路径
```

从[pypi源](https://pypi.org/project/setuptools/#files)上下载`pip`，下载压缩版本，解压之后，打开windows的cmd，在cmd里面cd到pip-20.1.tar.gz解压（假设是这个版本）文件所在的路径中，安装pip:`python setup.py install`.

##### [](#2-切换pypi源)2. [切换pypi源](#切换pypi源)

默认的pypi源，由于服务器在国外，蜗牛一样的下载速度，试过的同学非常清楚；解决这个下载慢的问题，将默认的pypi源到国内的镜像；建议切换到阿里云的镜像，稳定快速；

| pypi源 | 地址 |
|---|---|
| 清华大学 | https://pypi.tuna.tsinghua.edu.cn/simple/ |
| 中国科大 | https://mirrors.ustc.edu.cn/pypi/web/simple/ |
| 豆瓣 | https://pypi.doubanio.com/simple/ |
| 阿里云 | https://mirrors.aliyun.com/pypi/simple/ |

打开cmd输入set，找到USERPROFILE所在的路径，这个路径一般是用户的目录;在这个路径下创建一个pip文件夹在文件夹里面创建一个pip.ini文件，文件里面写入镜像地址。
```
[global]
trusted-host=aliyun.com
index-url=https://mirrors.aliyun.com/pypi/simple
```

##### [](#3-安装virtualenv虚拟化环境)3. [安装virtualenv虚拟化环境](#安装virtualenv虚拟化环境)

会不会有人问为啥要安装虚拟化环境，这个根据个人喜好和使用习惯来定。有虚拟化环境之后可以创建多个不同的配置的环境，可以在虚拟化的环境中装各种不同场景不同配置的应用，相互不影响。linux/mac可参考：[Virtualenv 的理解和使用](https://www.v2ex.com/t/354625)
以管理员权限打开cmd，安装virtualenv，

- 安装virtualenv， `pip install virtualenv`;

- 在一个路径创建一个存放虚拟环境的路径，这里用了`E:\python_lib`这个路径

- 用命令`virtualenv  web`创建虚拟化环境;

- 激活该环境,window使用cd web进入当前目录下的web中，然后用`Scripts\activate`激活;
![image](/images/imglib/virtualenv.png)

##### [](#4-安装tensorflow)4. [安装tensorflow](#安装tensorflow)

由于笔者的pc是渣渣，这里只能安装cpu版本的tensorflow，进入想要安装的虚拟化环境之后，直接安装tensorflow
```
pip install tensorflow-cpu==2.1 指定到cpu版本的2.1版本，当然也可以不指定
```

![image](/images/imglib/tensorflow.png)
安装完成之后进入：进入python 输入：`import tensorflow as tf` 看是否正常，无报错说明可以安装好了。然后就可以嗨皮的使用tensorflow了。

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
