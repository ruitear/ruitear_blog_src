---
title: 搭建基于hexo的github博客系统
date: 2018-07-18 00:19:32
tags:
  - blog环境
categories:
  - 环境搭建
---

本文以`windows`为生产环境，介绍以`github`为免费仓库和服务器，以及`node.js`和`hexo`技术为支撑免费搭建个人博客的方法。如果想让博客链接到个人域名，那就需要去单独购买域名。

![hexo操作](/images/hexo个人博客/github博客.png)

##### [](#1-Git安装与github仓库配置仓库配置)1. [Git安装与github仓库配置仓库配置](#Git安装与github仓库配置仓库配置)

  Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。与常用的版本控制工具 `CVS`, `Subversion`等不同， 它采用了分布式版本库的方式， 不必服务器端软件支持。windows环境下git的安装配置以及github仓库的配置, 可参考如下博文: [**windows搭建git环境**](http://ruitear.com/2018/07/14/windows%E6%90%AD%E5%BB%BAgit%E7%8E%AF%E5%A2%83/)

##### [](#2-Node-Js与Hexo安装)2. [Node.Js与Hexo安装](#Node.Js与Hexo安装)

  `Hexo`使用`Markdown`（或其他渲染引擎）解析文章，生成静态网页, 是搭建博客环境的基础环境，hexo依赖node.js；因此，安装`hexo`前必须安装`Node.js`和`Git`。

###### [](#2-1-下载-安装Node-js)2.1 [下载/安装Node.js](#下载-/-安装hexo)

    去[**官方**](https://nodejs.org/en/)下载最的`.msi`的新可靠版本，双击安装；

###### [](#2-2-下载-安装hexo)2.2 [下载/安装hexo](#下载-/-安装hexo)

    安装完`git`和`node.js`之后，在本地创建一个路径用来保存`hexo`，其实也就是自己的本地博客代码库，比如`E:\github\blog`；然后，打开安装的`git bash`切换到刚刚创建的目录，输入`npm install -g hexo-cli`安装hexo；

> 
**注**: `npm`是随同`Node.JS`一起安装的包管理工具, 所以装好`node.js`就可以在`git bash`中使用`npm`工具，无需单独安装；

![hexo操作](/images/hexo个人博客/hexo操作.png)

##### [](#3-Hexo的基本配置)3. [Hexo的基本配置](#Hexo的基本配置)

  Hexo 作为一个快速、简洁且高效的博客框架。在我们下载hexo后，首先要做的是初始化hexo，告诉hexo在哪里保存博客网站

- 
**初始化hexo**  `$ hexo init [folder]`，没有folder属性就默认在hexo当前的路径建立并保存网站文件。查看`hexo`的基本命令可以参考[hexo指令](https://hexo.io/zh-cn/docs/commands.html)；

- 
**更改hexo主题**  笔者使用的[next模板主题](https://github.com/iissnan/hexo-theme-next)，首先切换到`hexo`的`themes`文件夹下，将主题下载下来，在`hexo`的文件中将原始的`theme`配置由`landscape`改为`next`；

```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

- 
**部署主题** hexo路径下的`git bash`输入: 

```
hexo clean; hexo generate  #清理缓存并生成文件
hexo deploy  #部署
```

- 
**配置主题**  next主题的配置文件是next文件夹下的`_config.yml`文件

- **写博文** `$ hexo new <title>` 使用默认属性在`source\_posts`路径下，生成一个`markdown`文件，然后就可以在这个文件中撰写自己的博客；

##### [](#4-绑定个人域名)4. [绑定个人域名](#绑定个人域名)

  **购买和注册**：目前国内阿里、腾讯和百度以及华为的云都有域名购买和注册的服务，笔者在腾讯云上注册了[ruitear.com](ruitear.com)这个域名。一个域名一年一般几十块钱, 还提供免费的dns域名解析服务。

- 配置`cname`文件：在项目的source文件夹下新建一个名为`CNAME`的文件，在里面添加你购买的域名，比如我添加的是[ruitear.com](ruitear.com)，有且只能添加一个。

- 将`github`博客关联到自己的域名：在域名解析的地方添加三条记录，如下图所示；
![image](/images/hexo个人博客/cname.png)

> 
注意：blog.github.io. 是我的博客仓库名；`192.30.252.153`,`192.30.252.154`是GitHub提供的IP地址。

> 

> 
- `www`：解析后的域名为 `www.你的域名`
> 
- `@`：直接解析主域名 你的域名
> 
- `*`：泛解析，匹配其他所有域名 `*.你的域名`
> 

---

##### [](#参考文献)[参考文献](参考文献)

[1] hexo中文文档：[https://hexo.io/zh-cn/docs/](https://hexo.io/zh-cn/docs/)
[2] Hexo搭建的GitHub博客之优化 [http://wangwlj.com/2017/09/09/blog-opti/](http://wangwlj.com/2017/09/09/blog-opti/)
[3] 在Github上利用hexo搭建个人博客 [https://www.jianshu.com/p/9df4aba9c25a](https://www.jianshu.com/p/9df4aba9c25a)

      
    

    

  
    

    
        
-------------本文结束感谢您的阅读-------------

    

  

    
    
    

    

    
      

        

  
坚持创作，坚持分享！

  
    打赏
  
  

    
      

        ![liurui 微信支付](/images/wechatpay.png)
        
微信支付

      

    

    
      

        ![liurui 支付宝](/images/alipay.png)
        
支付宝
